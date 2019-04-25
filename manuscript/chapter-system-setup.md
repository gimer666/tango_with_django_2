#Настраиваем Вашу систему {#chapter-system-setup}
Эта вспомогательная глава содержит руководства по настройке, дополняющие материал [главы, посвященной начальной настройке](#chapter-getting-ready). Мы приводим руководства для установки и настройки различных технологий, которые Вы будуте использовать в Танго с Django. Используйте тот раздел этой главы, который Вам нужен; нет необходимости в прочтении всей главы, если какая-то из описанных технологий уже установлена и работает у Вас.

I> ### Обобщенные рекомендации по настройке
I>
I> В этой главе приведены инструкции по настройке различных технологий, которые Вы будете использовать по ходу чтения пособия Танго с Django, и как нам кажется их можно использовать при работе с большинством систем, с которыми Вы столкнетесь. Тем не мнее каждый компьютер обладает своим собственным набором аппратного и программного обеспечения, причём могут быть установлеными различные версии одного и того же программного обеспечения. Эти различия приводят к тому, что создать универсальное руководство по настройке очень сложно.
I> 
I> Если Вы используете эту книгу при изучении нашего университетского курса, то возможно Вам были выданы инструкции по настройке Ваших лабораторных компьютеров. Используйте их, поскольку большая часть работы по настройке, скорее всего уже была выполнена за Вас.
I>
I> Тем не менее, если Вы работаете самостоятельно и безуспешно пытаетесь выполнить инструкции, приведенные в этой главе, мы рекомендуем Вам использовать Вашу любимую поисковую систему и найти решение проблемы в Интернете. Как правило достаточно скопировать и вставить сообщение об ошибке, с которой Вы столкнулись, при установке программного обеспечения. Вставив дословно сообщение, Вы скорее всего найдёте кого-то, кто столкнулся с той же проблемой -- используя причеденную в Интернете последовательность действий Вы вероятно решите свою проблему.

## Устанавливаем Python 3 и `pip` {#section-system-setup-python}
Как установить Python 3.7 на Ваш компьютер? В этом разделе дается ответ на этот вопрос. Как мы [уже говорили ранее](#chapter-getting-ready-python3), возможно Вы обнаружите, что на Вашем компьютере уже установлен Python. Если Вы используете дистрибутив Linux или macOS, он обязательно будет установлен. Некоторые из функциональных возможностей Вашей операционной системы [реализованы на Python](http://en.wikipedia.org/wiki/Yellowdog_Updater,_Modified), поэтому необходим интерпретатор языка! К сожалению, большинство современных операционных систем, которые поставляются с предустановленной Python, используют версию, которая намного старее, чем нам требуется. Исключением является Ubuntu, поставляемая с версией 3.6.5, которой должно быть достаточно для ваших нужд. Если Вам нужно установить версию 3.7, её необходимо поставить так, чтобы она не конфликтовала с уже ранее установленной версией.

Существует множество способов установки Python. Здесь мы покажем наиболее распространенные методы, которые Вы можете использовать в macOS от Apple, различных дистрибутивах Linux и Windows 10. Выберите раздел, подходящий для вашей операционной системы и продолжайте чтение с него. ОБратите внимание, что мы поощеряем использование [менеждеров пакетов](https://en.wikipedia.org/wiki/Package_manager), где это возможно. Это позволяет легко поддерживать и обновлять программного обеспечение (при необходимости).

### Apple macOS
Простейший способ установить Python 3 для macOS - это загрузить файл образа `.dmg` с [официального сайта Python](https://www.python.org/downloads/mac-osx/). В этом случае Вам будет доступен пошаговый интерфейс установки, который упрощает процесс настройки. Если Ваша среда разработки не будет усложняться (т. е., будет использовать только Python), то стоит использовать эту возможность. Просто скачайте файл и после установки Python 3 должен быть доступен в терминале Вашего Mac!

However, [package managers make life easier](https://softwareengineering.stackexchange.com/questions/372444/why-prefer-a-package-manager-over-a-library-folder) when development steps up and involves a greater number of software tools. Installing a package manager makes it easy to maintain and update software on your computer -- and even to install new software, too. macOS does not come preinstalled with a package manager, so you need to download and install one yourself. If you want to go down this route, we'll introduce you to *MacPorts*, a superb package manager offering a [large host of tools](https://www.macports.org/ports.php) for you to download and use. We recommend that you follow this route. Although more complex, the end result will be a complete development environment, ready for you to get coding.

A prerequisite for using MacPorts is that you have Apple's *Xcode* environment installed. This download is several gigabytes in size. The easiest way to acquire this is through the App Store on your macOS installation. You'll need your Apple account to download that software. Once XCode has been installed, follow the following steps to setup MacPorts.

1. Verify that XCode is installed by launching it. You should see a welcome screen. If you see this, quit the app.
2. Open a Terminal window. Install the XCode command line tools by entering the command 
   
   {lang="bash",linenos=off}
    $ xcode-select --install
   
   This will download additional software tools that will be required by XCode and additional development software that you later install.
   
3. Agree to the XCode license, if you have not already. You can do this by entering the command 
   
   {lang="bash",linenos=off}
    $ xcode-build license
   
   Read the terms to the bottom of the page, and type `Y` to complete -- but only if you agree to the terms!
   
4. From [the MacPorts installation page](https://www.macports.org/install.php), download the MacPorts installer for your correct macOS version.
5. On your Mac's Finder, open the directory where you downloaded the installer to, and double-click the file to launch the installation process.
6. Follow the steps, and provide your password to install the software.
7. Once complete, delete the installer file -- you no longer require it. Close down any Terminal windows that are still open.

Once the MacPorts installation has been completed, installing Python is straightforward.

1. Open a new Terminal window. It is important that you launch a new window after MacPorts installation!
2. Enter the command
   
   {lang="bash",linenos=off}
    $ sudo port install python37
   
   After entering your password, this will take a few minutes. Several dependencies will be required -- agree to these being installed by responding with `Y`.
   
3. Once installation completes, activate your new Python installation. Enter the command
   
   {lang="bash",linenos=off}
    $ sudo port select --set python python37
   
4. Test that the command succeeds by issuing the command `$ python`. You should then see the interpreter for Python 3.7.2 (or whatever version you just installed).

Once this has been completed, Python has been successfully installed and is ready to use. However, we still need to setup virtual environments to work with your installation.

1. Enter the following commands to install `virtualenv` and helper functions provided in the user-friendly wrapper.
   
   {lang="bash",linenos=off}
    $ sudo port install py37-virtualenv
    $ sudo port install py37-virtualenvwrapper
   
2. Activate `py37-virtualenv` with the following command.
   
   {lang="bash",linenos=off}
    $ sudo port select --set virtualenv virtualenv37

3. Edit your `~/.profile` file. Add the following four lines at the end of the file.
   
   {lang="bash",linenos=off}
    export VIRTUALENVWRAPPER_PYTHON='/opt/local/bin/python3.7'
    export VIRTUALENVWRAPPER_VIRTUALENV='/opt/local/bin/virtualenv-3.7'
    export VIRTUALENVWRAPPER_VIRTUALENV_CLONE='/opt/local/bin/virtualenv-clone-3.7'
    source /opt/local/bin/virtualenvwrapper.sh-3.7

4. Save the file and close all open Terminals. Open a new Terminal. Everything should now be working and ready for you to use.


I> ### Installating Additional Software with MacPorts
I>
I> MacPorts provides an extensive, preconfigured library of open-source software suited specifically for development environments. When you need something new, it's a cinch to install. **For example,** you want to install the *LaTeX* typesetting system, search [the MacPorts ports list](https://www.macports.org/ports.php) -- the resultant package name being `texlive-latex`. This could then be installed with the command `$ sudo port install texlive-latex`. All software that LaTeX is dependent upon is also installed. This saves you significant amounts of time trying to find all the right bits of software to make things work.
I>
I> To view the packages MacPorts has already installed on your system, issue the command `$ port list installed`. You will see `python37` listed!

### Дистрибутивы Linux
There are many different ways in which you can download, install and run an updated version of Python on your Linux distribution. Methodologies unfortunately vary from distribution to distribution. To compound this, almost all distributions of Linux don't have a precompiled version of Python 3.7 ready for you to download and start using (at the time of writing), although the latest release of Ubuntu use Python 3.6 (which is sufficient).

If you do choose to install a new version of Python, we've put together a series of steps that you can follow. These will allow you to install Python 3.7.2 from scratch. The steps have been tested thoroughly in Ubuntu 18.04 LTS; other distributions should also work with minor tweaks, especially in relation to the package manager being used in step 1. A cursory search on your favorite search engine should reveal the correct command to enter. For example, on a *Red Hat Enterprise Linux* installation, the system package manager is `yum` instead of `apt`.

W> ## Assumption of Knowledge
W>
W> In order to complete these steps, we assume you know the basics for Bash interaction, including what the tilde (`~`) means, for example. Be careful with the `sudo` command, and do not execute it except for the steps we list requiring it below.

1. Install the required packages for Python to be built successfully. These are listed below, line by line. These can be entered into an Ubuntu Terminal as-is; slight modifications will be required for other Linux distributions.
   
   {lang="bash",linenos=off}
    $ apt install wget
    $ apt install build-essential
    $ apt install libssl-dev
    $ apt install libffi-dev
    $ apt install python-dev
    $ apt install zlib1g-dev
    $ apt install libbz2-dev
    $ apt install libreadline-dev
    $ apt install libsqlite3-dev

2. Once the above packages are installed, download the source for Python 3.7.2. We create a `pytemp` directory to download the file to. We'll delete this once everything has completed.
   
   {lang="bash",linenos=off}
    $ mkdir ~/pytemp
    $ cd ~/pytemp
    $ wget https://www.python.org/ftp/python/3.7.2/Python-3.7.2.tgz

3. Extract the `.tgz` file.
   
   {lang="bash",linenos=off}
    $ tar zxf Python-3.7.2.tgz
    $ cd Python-3.7.2

4. Configure the Python source code for your computer, and build it. `altinstall` tells the installer to install the new version of Python to a different directory from the prexisting version of Python on your computer. You'll need to enter your password for the system to make the necessary changes. This process will take a few minutes, and you'll see a lot of output. Don't panic. This is normal. If the build fails, you haven't installed all of the necessary prerequisites. Check you have installed everything correctly from step 1, and try again.
   
   {lang="bash",linenos=off}
    $ sudo ./configure --enable-optimizations
    $ sudo make altinstall

5. Once complete, delete the source files. You don't need them anymore.
   
   {lang="bash",linenos=off}
    $ cd ~
    $ rm -rf ~/pytemp

6. Attempt to run the new installation of Python. You should see the interpreter prompt for version 3.7.2, as expected.
   
   {lang="bash",linenos=off}
    $ python3.7
    Python 3.7.2 (default, Jan. 8 2019, 20:05:08)
    [GCC 7.3.0] on linux
    Type "help", "copyright", "credits" or "license" for more information
    >>> quit()
    $

7. By default, Python executables install to `/usr/local/bin`. Check this is correct for you. If not, run the `which` command to find out where it is installed. You'll need this path later.
   
   {lang="bash",linenos=off}
    $ which python3.7
    /usr/local/bin/python3.7

8. Install `virtualenv` and `virtualenvwrapper` for your new Python installation.
   
   {lang="bash",linenos=off}
    $ pip3.7 install virtualenv
    $ pip3.7 install virtualenvwrapper

9. Modify your `~/.bashrc` file, and include the following lines at the very bottom of the file. Note that if you are not using Ubuntu, you might need to edit `~/.profile` instead. Check the documentation of your distribution for more information. A simple text editor will allow you to do this, like `nano`.
   
   {lang="bash",linenos=off}
    EXPORT VIRTUALENVWRAPPER_PYTHON=/usr/local/bin/python3.7
    source /usr/local/bin/virtualenvwrapper.sh

10. Restart your Terminal. Python 3.7.2 will now be setup and ready for you to use, along with the `pip` and `virtualenv` tools.


### ОС Windows {#section-system-setup-python-windows}
По умолчанию, Microsoft Windows не имеет установленных в ней версий Python. Это означает, что не будет конфликтов с уже установленными версиями; достаточно просто осуществить установку с нуля. Вы можете загрузить 64-битную или 32-битную версию Python с [официального сайта Python](http://www.python.org/download/). Если Вы не уверены какую версию скачать, Вы можете определить является ли Ваш компьютер 32- или 64-разрядным, воспользовавшись инструкциями [на сайте Microsoft](https://support.microsoft.com/en-gb/help/13443/windows-which-operating-system).

1. Загрузите необходимый установщик с [официального сайта Python](http://www.python.org/download/). На момент написания - последней вышедшей версией была 3.7.2.
2. Запустите установщик. Убедитесь, что Вы отметили флажок, добавляющий Python 3.7 в `PATH`. Скорее всего Вы захотите установить Python для всех пользователей компьютера. Для этого выберите опцию `Customize`.
3. Продолжите установку с выбранными в настоящий момент флажками, выбрав `Next`.
4. Убедитесь, что флажок позволяющий установить Python для всех пользователей выбран. Посмотрите как изменится место установки. На [рисунке ниже](#fig-ch4setup-pywin-3) приведен пример.
5. Нажмите `Next`, чтобы установить Python. Вам нужно будет дать установщику необходимые привилегии для установки программного обеспечения.
6. Закройте установщик после завершения установки и удалите загруженный файл - он Вам больше не нужен.

После того как установка будет завершена, Вы станете обладателем рабочей версии Python 3.7. Следуя приведенным выше инструкциям, Python 3.7 устанавливается в каталог `C:\Program Files\Python37`. Если Вы выбрали все настройки правильно, переменная окружения `PATH`, используемая Windows, также должна быть обновлена и включать путь к каталогу с только что установленной версией Python. Чтобы проверить это, запустите окно командной строки и введите `$ python`. Запустите команду на выполнение. Вы должны увидеть запуск интерпретатора Python, как показано на [скриншоте ниже](#fig-ch4setup-pywin-4). Если этого не произошло, проверьте, правильно ли установлена переменная окружения `PATH`, используя [онлайн-руководство](https://www.architectryan.com/2018/03/17/add-to-the-path-on-windows-10/).

Как только Вы убедитесь, что Python установлен правильно, Вам нужно установить поддержку для виртуального окружения. Выполните следующие две команды, а затем перезапустите все открытые окна командной строки.

{lang="bash",linenos=off}
    $ pip install virtualenv
    $ pip install virtualenvwrapper-win

После завершения установки всё должно быть настроено и готово к использованию.

{id="fig-ch4setup-pywin-3"}
![Настраиваем Python 3.7.2 на Windows 10 x64 -- устанавливаем Python для всех пользователей.](images/chsetup-pywin-3.png)

## Виртуальные окружения {#section-system-setup-virtualenv}
По умолчанию, когда Вы устанавливаете программное обеспечение для Python, оно устанавливается *для всей системы*. Все приложения Python могут видеть новое программное обеспечение и использовать его. Однако если устанавливать программное обеспечение могут возникнуть проблемы. [Ранее в книге](#chapter-getting-ready-venv), мы рассматривали случай, когда двум программам требовались две разные версии одной и той же зависимости. Эту проблему не так просто решить, поскольку Вы обычно не можете просто установить две различные версии одного и того же программного обеспечения в Вашу рабочую среду Python!

Решением этой проблемы является использование *виртуального окружения*. Используя виртуальное окружение, для каждой программы, которую Вы хотите запустить, может быть создана своё собственное окружение и по определению, собственный набор установленных зависимостей. Если у Вас есть `ProjectA`, требующий Django 1.11, и `ProjectB`, требующий Django 2.1, то Вы можете создать виртуальное окружение для каждого проекта с установленными пакетами.

Для управления виртуальными окружениями мы будем использовать четыре основные команды, приденные ниже.

* `mkvirtualenv <name>` создаёт и активирует новое виртуальное окружение с названием `<name>`.
* `workon <name>` включает виртуальное окружение с названием `<name>`.
* `deactivate` отключает виртуальное окружение, которое Вы используете в настоящий момент.
* `rmvirtualenv <name>` удаляет виртуальное окружение с названием `<name>`.
* `lsvirtualenv` выводит все созданные пользователем виртуальные окружения.

Следуя приведенными выше примерам, мы можем создать окружение для каждого проекта, установив необходимое программное обеспечение в соответствующее окружение. Для `ProjectA` окружение называется `projAENV`, а для `ProjectB` - `projBENV`. Обратите внимание, что для установки программного обеспечения в соответствующих окружениях, мы используем `pip`. Команды, используемые для `pip` [приведены ниже.](#section-system-setup-pip)

{lang="bash",linenos=off}
    $ mkvirtualenv projAENV
    (projAENV) $ pip install Django==1.11
    (projAENV) $ pip freeze
    Django==1.11
    
    (projAENV) $ deactive
    $ pip
    <Command not found>
    
    $ workon projAENV
    $ pip freeze
    Django=1.11
    
    (projAENV) $ cd ProjectA/
    (projAENV) $ python manage.py runserver
    ...

Приведенные выше фрагменты кода создают новое виртуальное окружение, `projAENV`. Затем мы устанавливаем Django 1.11 в это виртуальное окружение прежде чем выполнить `pip freeze`, чтобы вывести список установленных пакетов, подтверждая, что Django был установлен. Затем мы деактивируем виртуальное окружение. После этого команда `pip` не может быть найдена, поскоьку мы больше не в виртуальном окружении! Снова включив виртуальное окружение, с помощью `workon`, мы можем опять найти наш пакет Django. Последние две команды запускают сервер разработки Django для `ProjectA`.

Затем мы можем создать второе виртуальное окружение, `projBENV`.

{lang="bash",linenos=off}
    $ mkvirtualenv projBENV
    (projBENV) $ pip install Django==2.1
    (projBENV) $ pip freeze
    Django==2.1
    
    (projBENV) $ cd ProjectA/
    (projBENV) $ python manage.py runserver
    <INCORRECT PYTHON VERSION!>
    
    (projBENV) $ workon projAENV
    (projAENV) $ python manage.py runserver
    
    (projAENV) $ workon projBENV
    (projBENV) $ cd ProjectB/
    $ python manage.py runserver
    ...

Мы создаем наше новое окружение с помощью команды `mkvirtualenv`. При этом создаётся и активируется `projBENV`. Однако при попытке запустить код для `ProjectA`, мы получаем ошибку! Мы используем не то виртуальное окружение. Переключившись на `projAENV`, используя `workon projAENV`, мы сможем запустить программное обеспечение без ошибок. Это показывает возможности виртуальных окружений и преимущества при их использовании. [Дополнительную информацию по их использованию можно найти в Интернете](https://realpython.com/python-virtual-environments-a-primer/)

T> ## `workon` и `deactivate`
T>
T> Начните сеанс работы с виртуальным окружением, выполнив команду `workon`. Чтобы завершить сеанс работы - зайкройте его с помощью `deactivate`.
T>
T> Вы можете определить, активна ли виртуальное окружение по скобкам перед приглашением командной строки, например, `(envname) $`. Это означает, что в настоящее время включено виртуальное окружение `envname` с загруженными для него настройками. Выключите его с помощью команды `deactivate` и скобки пропадут.

T> ## Несколько версий Python
T>
T> Если у Вас установлено несколько версий Python, Вы можете выбрать какую версию Python использовать при создании виртуального окружения. Если у Вас установлен `python` (который запускает версию 2.7.15) и `python3` (который запускает -- 3.7.2), Вы можете выполнить следующую команду для создания виртуального окружения Python 3.
T>
T> {lang="bash",linenos=off}
T>  $ mkvirtualenv -p python3 someenv
T>  $ python
T>  $ Python 3.7.2
T>  >>>
T>
T> Обратите внимание, что когда Вы включаете ваше виртуальное окружение, команда, которую Вы вводите для запуска Python -- просто `python`. То же самое относится к `pip` -- если Вы запускаете `pip3` вне виртуального окружения, то внутри виртуального окружения нужно использовать команду `pip`.

## Использование `pip` {#section-system-setup-pip}
Менеджер пакетов Python очень прост в использовании и позволяет вам управлять различными пакетами (программным обеспечением) Python, которые Вы установили. Мы настоятельно рекомендуем Вам использовать `pip` вместе с виртуальным окружением, поскольку пакеты, установленные с помощью `pip` видны только внутри указанного виртуального окружения.

Когда Вы знаете какой пакет хотите установить, выполните команду `$ pip install <packagename>==<version>`. Обратите внимание, что версию указывать не обязательно; если Вы опустите версию для конкретного пакета, то будет установлена последняя доступная версия.

Вы можете найти имя нужного пакета, изучив [*список пакета PyPi*](https://pypi.org/), из которого `pip`загружает программное обеспечение. Воспользуйтесь поиском или просмотрите список, чтобы найти то, что Вы ищите. Как только Вы знаете название пакета, Вы можете ввести команду для установки в Вашем терминале или командной строке.

`pip` is also super useful for listing packages that are installed in your environment. This can be achieved through the `pip freeze` command. Sample output is issued below.

{lang="bash",linenos=off}
    (rangoenv) $ pip freeze
    Django==2.1.5
    Pillow==5.0.0
    pytz==2018.9

This shows that three packages are installed in a given environment: `Django`, `Pillow` and `pytz`. The output from this command is typically saved to a file called `requirements.txt`, stored in the root directory of a Python project. If somebody wants to use your software, they can then download your software -- complete with the `requirements.txt` file. Using this file, they can then create a new virtual environment set up with the required packages to make the software work.

If you find yourself in a situation like this, you run `pip` with the `-r` switch. Given a `requirements.txt` in a directory `downloaded_project` with only `pytz==2018.9` listed, an example CLI session would therefore involve something like the following.

{lang="bash",linenos=off}
    $ cd downloaded_project
    $ mkvirtualenv someenv
    (someenv) $ pip install -r requirements.txt
    ...
    (someenv) $ pip freeze
    pytz==2018.9

`pip install` installs packages from `requirements.txt`, and `pip freeze`, once everything has been installed, demonstrates that the packages have been installed correctly.

## Системы контроля версий
При разработке кода настоятельно рекомендуется хранить его в репозитории с системой контроля версий, такой как [SVN](http://subversion.tigris.org/) или [Git](http://git-scm.com/). Мы написали [главу о том, как использовать Git](#chapter-git), если Вы раньше не использовали Git и GitHub. Мы настоятельно рекомендуем вам создавать Git-репозитории для Ваших собственных проектов. Это может спасти Вас от многих проблем.

Для взаимодействия с Git репозиториями мы рекомендуем Вам использовать инструменты, доступные из командной строки. Они доступны с помощью команды `git`. В Windows Вам также нужно [загрузить Git с вебсайта Git](https://git-scm.com/download/win). Если Вы используете macOS or Linux, [на сайте Git есть установщики, которые Вы можете использоват для этих операционных систем](https://git-scm.com/downloads). Но лучше возьмите за привычку использовать менеджер пакетов для установки программного обеспечения. Как правило это рекомендуемый способ загрузки и использования программного обеспечения, разработанного используя принципы проектирования UNIX (включая macOS).

Например, установить Git можно, введя команду `$ sudo apt install git`. Позвольте программному обеспечению загрузиться, и менеджер пакетов `apt` позаботится обо всем остальном. Если Вы установили MacPorts на Вашу macOS, как описано выше, у Вас уже будет установлен Git, поскольку он является частью инструментов разработчика для командной строки Apple XCode.

После установки, введите `git`, чтобы отобразить команды, которые Вы можете использовать, как показано в примере ниже.

{lang="bash",linenos=off}
    $ git
    usage: git [--version] [--help] [-C <path>] [-c name=value]
           [--exec-path[=<path>]] [--html-path] [--man-path] [--info-path]
           [-p | --paginate | --no-pager] [--no-replace-objects] [--bare]
           [--git-dir=<path>] [--work-tree=<path>] [--namespace=<name>]
           <command> [<args>]