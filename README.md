# Zabbix VmBix Module ![alt text](https://travis-ci.org/dav3860/vmbix_zabbix_module.svg?branch=master "Build Status")

## Description
ZO Zabbix 2.2+ vem com suporte de módulos carregáveis ​​para estender o agente eo servidor Zabbix sem sacrificar o desempenho.

Um módulo carregável é basicamente uma biblioteca compartilhada usada pelo servidor Zabbix ou agente e carregado na inicialização. A biblioteca deve conter certas funções, de modo que um processo Zabbix pode detectar que o arquivo é de fato um módulo com o qual ele pode carregar e trabalhar.

Eu criei um módulo para consultar o VmBix daemon, usado para monitorar uma infra-estrutura VMWare com Zabbix.

## Install
Obtenha a última versão [aqui](https://bintray.com/dav3860/generic/vmbix-zabbix-module/view#files)
Por padrão, os pacotes irá criar um arquivo /etc/zabbix/zabbix_agentd.d/modules.conf para permitir que o módulo. Configurá-lo para o servidor ou o proxy, se necessário (consulte a seção Configurar).

## Ou criar a partir da fonte
You need to download the Zabbix source and configure the environment :

```
cd <source_zabbix>
```
For Zabbix 2.4.x :
```
./configure
```
For Zabbix 3+ :
```
./configure --with-openssl
```
Then :
```
cd <source_zabbix>/src/modules/
```

And you should create a new directory with this git repo content :
```
git clone https://github.com/dav3860/vmbix_zabbix_module.git
cd vmbix_zabbix_module
```

After that, compile the module :

### For Zabbix 2.4 :
```
make vmbix-2.4
```

### For Zabbix 3.0 :
```
make vmbix-3.0
```

### For Zabbix 3.2 :
```
make vmbix-3.2
```

This will create the vmbix.so file. Put it into a /usr/lib/zabbix/modules directory for example.

## Configure

**For an agent :** `/etc/zabbix/zabbix_agentd.conf`

**For a server :** `/etc/zabbix/zabbix_server.conf`

**For a proxy :** `/etc/zabbix/zabbix_proxy.conf`

```
LoadModulePath=/usr/lib/zabbix/modules
LoadModule=vmbix.so
```

Por padrão, os pacotes criarão um arquivo /etc/zabbix/zabbix_agentd.d/modules.conf com os parâmetros acima.

E reinicie o agente ou servidor / proxy.

Por padrão, o módulo consultará VmBix no localhost e na porta 12050. Você pode criar um arquivo de configuração /etc/zabbix/vmbix_module.conf se você quiser alterar isso. Um arquivo de configuração de exemplo é fornecido.

Você pode testá-lo como este, por exemplo, se você configurou um agente Zabbix para carregar o módulo:

```
[TEST zbx_vmbix]# zabbix_agentd -t vmbix[version]
2.3.0.59
[TEST zbx_vmbix]# zabbix_agentd -t vmbix[vm.guest.os,VM01]
vmbix[vm.guest.os,VM01]                  [s|Red Hat Enterprise Linux 6 (64 bits)]
```

Ou se VmBix estiver configurado para usar o UUID para fazer referência aos objetos (parâmetro useuuid):

```
[TEST zbx_vmbix]# zabbix_agentd -t "vmbix[vm.discovery,*]"
{
  "data": [
    {
      "{#VIRTUALMACHINE}": "VM01",
      "{#UUID}": "4214811c-1bab-f0fb-363b-9698a2dc607c"
    },
    {
      "{#VIRTUALMACHINE}": "VM01",
      "{#UUID}": "4214c939-18f1-2cd5-928a-67d83bc2f503"
    }
  ]
}
[TEST zbx_vmbix]# zabbix_agentd -t vmbix[vm.guest.os,4214811c-1bab-f0fb-363b-9698a2dc607c]
vmbix[vm.guest.os,VM01]                  [s|Red Hat Enterprise Linux 6 (64 bits)]
```

## Zabbix Templates
Sample templates using this loadable module are provided in the [VmBix repository](https://github.com/dav3860/vmbix/tree/master/zabbix).
