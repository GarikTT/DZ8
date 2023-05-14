Урок 8 - Загрузка системы.
Цели занятия –
объяснить как происходит загрузка системы, в чем разница между BIOS/UEFI;
настраивать GRUB2;
управлять initrd с помощью dracut;
работать с udev;
восстанавливать сломанный загрузчик.
Домашнее задание – сконфигурировать grub2 и изменить порядок и параметры загрузки.
Для выполнения домашнего задания используем методичку работа с загрузчиком https://drive.google.com/file/d/1-lfwAa6hOC-HVF2Agz9tj21vtKFyjDq7/view?usp=share_link
1.	Создаем и запускаем стандартный Vagrantfile для centos/7. Нажимаем «e». Получаем вывод – 1.jpg. Удаляем в параметрах запуска “console=tty0 console=ttyS0,115200n8” и добавляем в конец строки «rd.break» - 2.jpg
2.	Нажимаем «Ctrl-X». Загружаемся в режим «emergence mode». Выполняем по инструкции команды для смены пароля пользователя root -3.jpg
3.	Перезагружаем компьютер и входим с новым паролем – 4.jpg
4.	Аналогичным образом добавляем в конец строки параметров загрузчика «init=/bin/sh» – 5.jpg.
	Проводим операции по смене пароля при этом параметре – 6.jpg
	Перезагружаемся и входим с новым паролем. 7.jpg
5.	Меняем пароль третьим способом через «init=/sysroot/bin/sh». 8-12.jpg
6.	Для выполнения следующей части загружаем Vagrantfile из третьего домашнего задания и выполняем действия -
	1.	script --timing=time_homework_log homework.log
	2.	vagrant up
	3.	vagrant ssh
	4.	sudo -i
	5.	vgs
	6.	cat -n /etc/fstab
	7.	cat -n /etc/default/grub
	8.	cat -n /boot/grub2/grub.cfg
	9.	vgrename VolGroup00 Garik
	10.	Правим /etc/fstab, /etc/default/grub, /boot/grub2/grub.cfg. Везде заменяем старое название на Garik
	11.	sed -i 's*VolGroup00*Garik*g' /etc/fstab
	12.	sed -i 's*VolGroup00*Garik*g' /etc/default/grub
	13.	sed -i 's*VolGroup00*Garik*g' /boot/grub2/grub.cfg
	14.	cat -n /etc/fstab
	15.	cat -n /etc/default/grub
	16.	cat -n /boot/grub2/grub.cfg
	17.	mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)
	18.	mkdir /usr/lib/dracut/modules.d/01test
	19.	echo '#!/bin/bash
	check()
	{
    return 0
	}
	depends()
	{
    return 0
	}
	install()
	{ 
	inst_hook cleanup 00 "${moddir}/test.sh"
	}' > /usr/lib/dracut/modules.d/01test/module-setup.sh
	20.	ls -la /usr/lib/dracut/modules.d/01test
	21.	cat /usr/lib/dracut/modules.d/01test/module-setup.sh
	22.	cat > /usr/lib/dracut/modules.d/01test/test.sh
	23.	Вставляем -
	#!/bin/bash

exec 0<>/dev/console 1<>/dev/console 2<>/dev/console
cat <<'msgend'
Hello! You are in dracut module!
 ___________________
< I'm dracut module >
 -------------------
   \
    \
        .--.
 |o_o |
       |:_/ |
      //   \ \
     (|     | )
    /'\_   _/`\
    \___)=(___/
msgend
sleep 10
echo " continuing...."
	24.	Жмем CTRL-D
	25.	cat -n /usr/lib/dracut/modules.d/01test/test.sh
	26.	dracut -f -v
	27.	lsinitrd -m /boot/initramfs-$(uname -r).img | grep test
	28.	reboot
	29.	exit
	Получаем файлы time_homework_log и homework.log с описанными действиями и фиксируем перезагрузку – 13.jpg.
	В файле дз1.docx описанные действия с рисунками.

	30. scriptreplay --timing=time_homework_log homework.log -d 20
