---
tags: [terraform, iac, devops]
---

# 🏗️ Terraform

Связано: [[⚙️Ansible]] · [[☸️Kubernetes]]

Infrastructure as Code для **провижининга** ресурсов (в отличие от Ansible, который больше про конфигурацию уже существующих машин).

## Ключевые понятия

- **Provider** — плагин для работы с конкретным API (AWS, GCP, Yandex Cloud, Cloudflare, Docker...).
- **Resource** — объект инфраструктуры (VM, сеть, DNS-запись).
- **State** (`terraform.tfstate`) — снимок реального состояния инфраструктуры, с которым Terraform сверяется.
- **Plan** — dry-run: что изменится.
- **Module** — переиспользуемый набор ресурсов.

## Пример: VM в Yandex Cloud

```hcl
terraform {
  required_providers {
    yandex = {
      source  = "yandex-cloud/yandex"
    }
  }
}

provider "yandex" {
  token     = var.yc_token
  cloud_id  = var.cloud_id
  folder_id = var.folder_id
  zone      = "ru-central1-a"
}

resource "yandex_compute_instance" "web" {
  name        = "web-server"
  platform_id = "standard-v3"

  resources {
    cores  = 2
    memory = 2
  }

  boot_disk {
    initialize_params {
      image_id = "fd8xxxxxxxxxxxxxxxx" # Ubuntu 22.04
    }
  }

  network_interface {
    subnet_id = yandex_vpc_subnet.default.id
    nat       = true
  }
}
```

## Базовый workflow

```bash
terraform init          # скачать провайдеры/модули
terraform validate      # проверить синтаксис
terraform plan           # что изменится
terraform apply          # применить
terraform apply -auto-approve
terraform destroy        # снести всё, что описано
terraform fmt             # форматирование .tf файлов
```

## State — важные моменты

- Никогда не редактируй `.tfstate` руками.
- В команде — храни state удалённо (S3 / Terraform Cloud / GCS) с блокировкой (`state locking`), а не в git.
- `.gitignore`: обязательно добавь `*.tfstate`, `*.tfstate.backup`, `.terraform/`.

```hcl
terraform {
  backend "s3" {
    bucket = "my-tfstate-bucket"
    key    = "prod/terraform.tfstate"
    region = "eu-central-1"
  }
}
```

## Переменные и outputs

```hcl
variable "instance_count" {
  type    = number
  default = 2
}

output "web_ip" {
  value = yandex_compute_instance.web.network_interface[0].nat_ip_address
}
```

## Terraform vs Ansible

| | Terraform | Ansible |
|---|---|---|
| Основная задача | создать инфраструктуру | настроить уже существующие машины |
| Модель | декларативная, state-based | процедурная (задачи по порядку) |
| Типичный кейс | "создай мне 3 VM и сеть" | "поставь nginx и задеплой конфиг" |

На практике часто используют вместе: Terraform создаёт VM → Ansible их конфигурирует.

#terraform #iac #devops
