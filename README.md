# Домашнее задание к занятию "`Введение в Terraform`" - `Фумелли Петр`

### Задание 1

* В каком terraform-файле допустимо сохранить личную, секретную информацию (логины, пароли, ключи, токены и т.д.)?

  * Ответ: own secret vars store. personal.auto.tfvars

* Найдите в state-файле секретное содержимое созданного ресурса random_password, пришлите в качестве ответа конкретный ключ и его значение

  * Ответ: "result": "x0pZDBmJRW9VaD6s"

* Объясните, в чём заключаются намеренно допущенные ошибки

  * Ответ:
    * docker_image без имени - Terraform требует обязательно указать имя ресурса
    * ошибка имя контейнера 1nginx - нельзя начинать имя ресурса с цифры
    * ссылка на несуществующий ресурс random_string_FAKE
    * опечатка в resulT
    * обе секции закомментированы — ничего не создаётся

* Выполните код. В качестве ответа приложите: исправленный фрагмент кода и вывод команды

  * Ответ:

```tf
resource "random_password" "random_string" {
  length      = 16
  special     = false
  min_upper   = 1
  min_lower   = 1
  min_numeric = 1
}

resource "docker_image" "nginx" {
  name         = "nginx:latest"
  keep_locally = true
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.image_id
  name  = "example_${random_password.random_string.result}"

  ports {
    internal = 80
    external = 9090
  }
}

```

![alt text](https://github.com/PeterFumelli/terraform-intro/blob/master/img/docker-ps.png)

* Замените имя docker-контейнера в блоке кода на `hello_world`. Объясните, в чём может быть опасность применения ключа `-auto-approve`

  * Ответ: Terraform не спрашивает подтверждения перед применением изменений. Удобен при тестах/демонстрациях, чтобы не тратить время на подтверждения

![alt text](https://github.com/PeterFumelli/terraform-intro/blob/master/img/docker-ps2.png)

* Уничтожьте созданные ресурсы с помощью terraform. Приложите содержимое файла `terraform.tfstate`

  * Ответ:

```tf
{
  "version": 4,
  "terraform_version": "1.12.2",
  "serial": 11,
  "lineage": "c46e9d65-b21e-6032-81c4-1e84b3f868c3",
  "outputs": {},
  "resources": [],
  "check_results": null
}
```

* Объясните, почему при этом не был удалён docker-образ `nginx:latest`

  * Ответ: в docker_image указано `keep_locally = true` “If true, then the image will remain on the local system after Terraform destroys the resource.”
