# В данном репозитории собраны некоторые решения из курса [Программирование на Golang](https://stepik.org/course/54403/promo) на платформе Stepik

Проверка решений на Stepik имеет свои особенности и некоторые задания имеют формат "main скрыт, напишите функцию с таким-то названием", поэтому некоторые решения являются только _частью_ кода.

**!** Если вы нашли эту страницу в поисках готовых ответов, то прошу все-таки постараться решить самому и только потом сравнить с моими решениями! **!**

## Задание 3.5.13
### Поиск файла в заданном формате и его обработка

>Данная задача поможет вам разобраться в пакете encoding/csv и path/filepath, хотя для решения может быть использован также пакет archive/zip (поскольку файл с заданием предоставляется именно в этом формате).
>
>В тестовом архиве, который вы можете скачать из нашего [репозитория](https://github.com/semyon-dev/stepik-go/tree/master/work_with_files/task_csv_1) на github.com, содержится набор папок и файлов. Один из этих файлов является файлом с данными в формате CSV, прочие же файлы структурированных данных не содержат.
>
>Требуется найти и прочитать этот единственный файл со структурированными данными (это таблица 10х10, разделителем является запятая), а в качестве ответа необходимо указать число, находящееся на 5 строке и 3 позиции (индексы 4 и 2 соответственно).
>
Решение:
```go
package main

import (
	"bufio"
	"fmt"
	"io"
	"os"
)

func main() {
	txt, err := os.Open(".../stepik_3.5.13/taskdata.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	defer txt.Close()

	reader := bufio.NewReader(txt)
	var count int = 0
	for {
		num_0, err := reader.ReadString(59)
		if err != nil {
			if err == io.EOF {
				break
			} else {
				fmt.Println(err)
				return
			}
		}
		count++
		if len(num_0) == 2 {
			fmt.Println("Строка:", num_0, " Позиция:", count)
		}
	}
} 
```
## Задание 3.6.6
### JSON
>На стандартный ввод подаются данные о студентах университетской группы в формате JSON:
>
```JSON
 {
"ID":134,
"Number":"ИЛМ-1274",
"Year":2,
"Students":[
{
"LastName":"Вещий",
"FirstName":"Лифон",
"MiddleName":"Вениаминович",
"Birthday":"4апреля1970года",
"Address":"632432,г.Тобольск,ул.Киевская,дом6,квартира23",
"Phone":"+7(948)709-47-24",
"Rating":[1,2,3]
},
{
// ...
}
]
}
```
>В сведениях о каждом студенте содержится информация о полученных им оценках (Rating). Требуется прочитать данные, и рассчитать среднее количество оценок, полученное студентами группы. Ответ на задачу требуется записать на стандартный вывод в формате JSON в следующей форме:

```JSON 
{
"Average": 14.1
}
```
Решение:
```go
package main

import (
	"encoding/json"
	"fmt"
	"os"
)

type Group struct {
	ID       string
	Number   string
	Year     int
	Students []Student
}

type Student struct {
	LastName   string
	FirstName  string
	MiddleName string
	Birthday   string
	Address    string
	Phone      string
	Rating     []float32
}

type Rating struct {
	Average float32
}

func main() {

	var GroupGoStruct Group
	json.NewDecoder(os.Stdin).Decode(&GroupGoStruct)

	var countStudents float32
	var countMarks float32
	for _, v := range GroupGoStruct.Students {
		countStudents++
		for _, _ = range v.Rating {
			countMarks++
		}
	}
	var answer Rating
	answer.Average = countMarks / countStudents
	data, err := json.MarshalIndent(answer, "", "    ")
	if err != nil {
		fmt.Println(err)
	}
	fmt.Print(string(data))
}
```
## Задание 3.6.9
### JSON

>Данная задача ориентирована на реальную работу с данными в формате JSON. Для решения мы будем использовать справочник ОКВЭД (Общероссийский классификатор видов экономической деятельности), который был размещен на web-портале data.gov.ru.
>
>Необходимая вам информация о структуре данных содержится в файле structure-20190514T0000.json, а сами данные, которые вам потребуется декодировать, содержатся в файле data-20190514T0100.json. Файлы размещены в [нашем репозитории на github.com](https://github.com/semyon-dev/stepik-go/tree/master/work_with_json).
>
>Для того, чтобы показать, что вы действительно смогли декодировать документ вам необходимо в качестве ответа записать сумму полей global_id всех элементов, закодированных в файле.

Решение:
```go
package main

import (
	"encoding/json"
	"fmt"
	"net/http"
)

type Global_ids struct {
	Id int `json:"global_id"`
}

func main() {
	inURL := "https://github.com/semyon-dev/stepik-go/raw/master/work_with_json/data-20190514T0100.json"
	data, _ := http.Get(inURL)
	var base []Global_ids

	json.NewDecoder(data.Body).Decode(&base)

	var count int

	for _, v := range base {
		count += v.Id
	}
	fmt.Println(count)
}
```
## Задание 3.7.4
### Работа с датой и временем
> На стандартный ввод подается строковое представление даты и времени определенного события в следующем формате:
>
>`2020-05-15 08:00:00`
>
>Если время события до обеда (13-00), то ничего менять не нужно, достаточно вывести дату на стандартный вывод в том же формате.
>
>Если же событие должно произойти после обеда, необходимо перенести его на то же время на следующий день, а затем вывести на стандартный вывод в том же формате.
>
>**Sample Input:**
>
>`2020-05-15 08:00:00`
> 
>**Sample Output:**
>
>`2020-05-15 08:00:00` 
> 
Решение:
```go
package main

import (
"bufio"
"os"
"fmt"
"time"
)

func main() {
layot := "2006-01-02 15:04:05"

	scanner := bufio.NewScanner(os.Stdin)
	scanner.Scan()
	input := scanner.Text()
    
	inTime, err := time.Parse(layot, input)
	if err != nil {
		panic(err)
	}
	
	if inTime.Hour()<13 {
        fmt.Println(string(inTime.Format(layot)))
    } else {
		inTime=inTime.Add(time.Hour*24)
        fmt.Println(string(inTime.Format(layot)))
	}
}
```
