# В данном репозитории собраны некоторые решения из курса [Программирование на Golang](https://stepik.org/course/54403/promo) на платформе Stepik

Проверка решений на Stepik имеет свои особенности и некоторые задания имеют формат "main скрыт, напишите функцию с таким-то названием", поэтому некоторые решения являются частью кода.

## Задание 3.5.13
### Поиск файла в заданном формате и его обработка

>Данная задача поможет вам разобраться в пакете encoding/csv и path/filepath, хотя для решения может быть использован также пакет archive/zip (поскольку файл с заданием предоставляется именно в этом формате).
>
>В тестовом архиве, который вы можете скачать из нашего [репозитория](https://github.com/semyon-dev/stepik-go/tree/master/work_with_files/task_csv_1) на github.com, содержится набор папок и файлов. Один из этих файлов является файлом с данными в формате CSV, прочие же файлы структурированных данных не содержат.
>
>Требуется найти и прочитать этот единственный файл со структурированными данными (это таблица 10х10, разделителем является запятая), а в качестве ответа необходимо указать число, находящееся на 5 строке и 3 позиции (индексы 4 и 2 соответственно).


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
## Задача 3.6.9
### JSON

>Данная задача ориентирована на реальную работу с данными в формате JSON. Для решения мы будем использовать справочник ОКВЭД (Общероссийский классификатор видов экономической деятельности), который был размещен на web-портале data.gov.ru.
>
>Необходимая вам информация о структуре данных содержится в файле structure-20190514T0000.json, а сами данные, которые вам потребуется декодировать, содержатся в файле data-20190514T0100.json. Файлы размещены в [нашем репозитории на github.com](https://github.com/semyon-dev/stepik-go/tree/master/work_with_json).
>
>Для того, чтобы показать, что вы действительно смогли декодировать документ вам необходимо в качестве ответа записать сумму полей global_id всех элементов, закодированных в файле.

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