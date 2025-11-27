 ```
 age-keygen -y ~/.config/sops/age/keys.txt
 ```
Вот это выведет публичный ключ для вставки в .sops

пример содержимого `.sops`

```
# .sops.yaml
creation_rules:
  - path_regex: secrets.yaml
    key_groups:
      - age:
          - age12345...6789

```

затем 

`sops secrets.yaml`

и вуаля - там уже образец примера файла будет.


