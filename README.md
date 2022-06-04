# devops-netology

add line

Т.к файл ".gitignore" находится в каталоге devops-netology/terraform,
правила из этого файла будут справедливы ко всем файлам и директориям только внутри каталога terraform.
 
**/.terraform/*		# Игнорируем все файлы и папки в любой директории с именем .terraform (в любом колличестве поддиректорий).

*.tfstate		# Игнорируем все файлы c расширением .tfstate во всех вложенных директориях.
*.tfstate.*		# Игнорируем все файлы содержащиие в части имнеи .tfstate и с любым расширением во всех вложенных директориях.  

crash.log		# Игнорируем все файлы с именем crash.log во всех вложенных директориях начиная с каталога terraform . 
crash.*.log		# Игнорируем файлы имя которых начинающийся на crash. с расширением .log (файл с любыми символами в имени между crash. и .log).

*.tfvars		# Игнорируем все файлы во всех вложенных директориях c расширением .tfvars
*.tfvars.json		# Игнорируем все файлы во всех вложенных директориях оканчивающиеся на .tfvars.json

override.tf		# Игнорируем все файлы во всех вложенных директориях начиная с каталога terraform с именем override.tf
override.tf.json	# Игнорируем все файлы во всех вложенных директориях с именем override.tf.json
*_override.tf		# Игнорируем все файлы во всех вложенных директориях с расширением tf и любыми символами в имени до _override
*_override.tf.json	# Игнорируем все файлы во всех вложенных директориях с расширением .json и любыми символами в имени до *_override.tf.json

.terraformrc		# Игнорируем все файлы во всех вложенных директориях с расширением .terraformrc
terraform.rc		# Игнорируем все файлы вво всех вложенных директория с именем terraform.rc
