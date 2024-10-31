# Создание локальных учетных записей

## HQ-RTR (EcoRouter)

```
conf t
username net_admin
password P@ssw0rd
role admin
activate
```

## BR-RTR (Eltex - vESR)

```
configure
username net_admin
password P@ssw0rd
privilege 15
end
!
commit
confirm
!
```
