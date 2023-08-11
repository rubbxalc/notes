# Remote Port Fowarding

# Tunel Windows (netsh)

```jsx
netsh interface portproxy add v4tov4 listenport=<puerto a escuchar> listenaddress=<direccion a escuchar> connectport=<puerto a conectar> connectaddress=<direccion a conectar>
```

## Listar conexiones

```jsx
netsh interface portproxy show all
```

## Resetear conexiones

```jsx
netsh interface portproxy reset
```