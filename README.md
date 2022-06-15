---
## Ответы к заданию по занятию «2.4. Инструменты Git»
---

### 1. Полный хеш и комментарий коммита, хеш которого начинается на 'aefea' найдены с помощью команды: git show aefea

<strong>Результат: aefead2207ef7e2aa5dc81a34aedf0cad4c32545 Update CHANGELOG.md</strong>

### 2. <strong>Коммит  85024d3100126de36331c6982bfaac02cdab9e76 соответствует тегу  v0.12.23</strong>

Результат получен с помощью команды: git show 85024d3 

### 3. <strong>У коммита b8d720 двое родителей с хешами: 56cd7859e05c36c06b56d013b55a252d0bb7e158 9ea88f22fc6269854151c571162c5bcf958bee2b</strong>
 
Результат получен с помощью команды: git show --pretty=%P b8d720

### 4. <strong>Хеши и комментарии между тегами v0.12.23 и v0.12.24</strong>

33ff1c03bb960b332be3af2e333462dde88b279e (tag: v0.12.24) v0.12.24
b14b74c4939dcab573326f4e3ee2a62e23e12f89 [Website] vmc provider links
3f235065b9347a758efadc92295b540ee0a5e26e Update CHANGELOG.md
6ae64e247b332925b872447e9ce869657281c2bf registry: Fix panic when server is unreachable
5c619ca1baf2e21a155fcdb4c264cc9e24a2a353 website: Remove links to the getting started guide's old location
06275647e2b53d97d4f0a19a0fec11f6d69820b5 Update CHANGELOG.md
d5f9411f5108260320064349b757f55c09bc4b80 command: Fix bug when using terraform login on Windows
4b6d06cc5dcb78af637bbb19c198faff37a066ed Update CHANGELOG.md
dd01a35078f040ca984cdd349f18d0b67e486c35 Update CHANGELOG.md
225466bc3e5f35baa5d07197bbc079345b77525e Cleanup after v0.12.23 release
85024d3100126de36331c6982bfaac02cdab9e76 (tag: v0.12.23) v0.12.23

Результат получен с помощью команды: git log v0.12.23 v0.12.24 --pretty=oneline

### 5. Искомый коммит: <strong>commit 8c928e83589d90a031f811fae52a81be7153e82f</strong>

Author: Martin Atkins <mart@degeneration.co.uk>

Date:   Thu Apr 2 18:04:39 2020 -0700

Результат получен с помощью команд: 

git log -S "func providerSource" --oneline (определяем где добавлена искомая строка)

В результате имеем 2 коммита:

5af1e6234 есть искомая строка c  func providerSource изменена   Apr 21 16:28:59 2020

8c928e835 есть искомая строка c  func providerSource добавлена  Apr 2 18:04:39 2020

Далее анализ коммитов "5af1e6234", "8c928e835"

git show 8c928e835 | grep "func providerSource(*"

Вывод команды: 

+func providerSource(services *disco.Disco) getproviders.Source {

git show 5af1e6234  | grep "func providerSource(*"

Вывод команды:

-func providerSource(services *disco.Disco) getproviders.Source {

+func providerSource(configs []*cliconfig.ProviderInstallation, services *disco.Disco) (getproviders.Source, tfdiags.Diagnostics) {

+func providerSourceForCLIConfigLocation(loc cliconfig.ProviderInstallationSourceLocation, services *disco.Disco) (getproviders.Source, tfdiags.Diagnostics) { 

### 6. <strong>Все коммиты в которых была добавлена / изменена функция globalPluginDirs</strong> 

125eb51dc Remove accidentally-committed binary

22c121df8 Bump compatibility version to 1.3.0 for terraform core release (#30988)

35a058fb3 main: configure credentials from the CLI config file

c0b176109 prevent log output during init

8364383c3 Push plugin discovery down into command package


Результат получен с помощью команд:

git log -S "globalPluginDirs" --oneline

### 7. <strong>Автор функции synchronizedWriters: Martin Atkins</strong>

commit 5ac311e2a91e381e2f52234668b49ba670aa0fe5

Author: Martin Atkins <mart@degeneration.co.uk>

Date:   Wed May 3 16:25:41 2017 -0700


Найдены все коммиты с помощью команды git log -S "synchronizedWriters" --oneline 

Далее анализ git show 5ac311e2a
