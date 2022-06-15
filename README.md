---
Ответы к заданию по занятию «2.4. Инструменты Git»
---

1. Полный хеш и комментарий коммита, хеш которого начинается на 'aefea' найдены с помощью команды: git show aefea

Результат: aefead2207ef7e2aa5dc81a34aedf0cad4c32545 Update CHANGELOG.md


2. Коммит  85024d3100126de36331c6982bfaac02cdab9e76 соответствует тегу  v0.12.23

Результат получен с помощью команды: git show 85024d3 

3. У коммита b8d720 двое родителей с хешами: 56cd7859e05c36c06b56d013b55a252d0bb7e158 9ea88f22fc6269854151c571162c5bcf958bee2b
 
Результат получен с помощью команды: git show --pretty=%P b8d720

4.

---
Перечислите хеши и комментарии всех коммитов которые были сделаны между тегами v0.12.23 и v0.12.24.
Найдите коммит в котором была создана функция func providerSource, ее определение в коде выглядит так func providerSource(...) (вместо троеточего перечислены аргументы).
Найдите все коммиты в которых была изменена функция globalPluginDirs.
Кто автор функции synchronizedWriters?
---
