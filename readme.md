Проявление бага в HOMM3:
При попытке зайти в сетевую игру по протоколу TCP/IP вываливается сообщение об ошибке.
______________________________________________
DirectPlay Error:

'Unknown error?'

File 'C:\Dev\Heroes 3 Exp 2\Game\Remote.cpp'
Line#1867
______________________________________________

Так-же баг проявляется в других сетевых играх/приложениях использующих  DirectPlay из DirectX 7.
(При этом DirectPlay из DirectX 8 может работать корректно, так как могут использоваться другие классы)

Баг связан с невозможностью инициализации COM объекта 
DirectPlayLobby ( CLSID: {2FE8F810-B2A5-11d0-A787-0000F803ABFC}, реализация COM-сервера в dplayx.dll ), используемого в DirectX 7 (DirectPlay) приложениях, работающих с сетью.

Вызов CoCreateInstance( CLSID_DirectPlayLobby, NULL, CLSCTX_INPROC_SERVER, IID_IDirectPlayLobby3A, (VOID**)&pDPLobby )
возвращает -1, что является признаком ошибки. Работа с DirectPlayLobby прекращается - программа выдаёт ошибку и завершает работу.

Проверить правильность диагноза можно на Samples из DirectxSDK7 - они все не работают.

COM объект регистрируется при установке DirectX, однако, (по невыясненным причинам) регистрация этого компонента может не произойти. Повторные установки и переустановки DirectX не исправляют ситуацию (возможно есть решение с полным удалением DirectX но в рамках Windows7 я не уверен в успешности данного решения).

описание для Windows7 x64

1) удалить dplayx.dll из windows\system32\
2) закинуть в C:\Windows\SysWOW64  dplayx.dll(если нету) и dplay.inf
3) произвести установку из dplay.inf (контекстное меню -> установить)
4) зарегистрировать библиотеку dplayx.dll 
(cmd от администратора -> C:\Windows\SysWOW64\regsvr32.exe dplayx.dll)
!! библиотека x86 регистрируется из C:\Windows\SysWOW64\regsvr32.exe !!
!! C:\Windows\system32\regsvr32.exe  регистрирует x64 библиотеки!!


Файлы:
testDPlay7.exe - утилита для тестирования CLSID_DirectPlay и CLSID_DirectPlayLobby. (может требовать MS redist 2010)
dplaunch.exe - утилита из DirectX7SDK samples
chatconnect.exe - утилита из DirectX7SDK samples


При регистрации COM объекта в реестре создаются следующие ключи:

	[HKEY_CLASSES_ROOT\Wow6432Node\CLSID\{2FE8F810-B2A5-11d0-A787-0000F803ABFC}]
	@="DirectPlayLobby Object"

	[HKEY_CLASSES_ROOT\Wow6432Node\CLSID\{2FE8F810-B2A5-11d0-A787-0000F803ABFC}\InProcServer32]
	@=hex(2):25,00,53,00,79,00,73,00,74,00,65,00,6d,00,52,00,6f,00,6f,00,74,00,25,\
	  00,5c,00,53,00,79,00,73,00,57,00,6f,00,77,00,36,00,34,00,5c,00,64,00,70,00,\
	  6c,00,61,00,79,00,78,00,2e,00,64,00,6c,00,6c,00,00,00
	"ThreadingModel"="Both"


	[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\DirectPlayLobby]
	@="DirectPlayLobby Object"

	[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\DirectPlayLobby\CLSID]
	@="{2FE8F810-B2A5-11d0-A787-0000F803ABFC}"

	[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Wow6432Node\CLSID\{2FE8F810-B2A5-11d0-A787-0000F803ABFC}]
	@="DirectPlayLobby Object"

	[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Wow6432Node\CLSID\{2FE8F810-B2A5-11d0-A787-0000F803ABFC}\InProcServer32]
	@=hex(2):25,00,53,00,79,00,73,00,74,00,65,00,6d,00,52,00,6f,00,6f,00,74,00,25,\
	  00,5c,00,53,00,79,00,73,00,57,00,6f,00,77,00,36,00,34,00,5c,00,64,00,70,00,\
	  6c,00,61,00,79,00,78,00,2e,00,64,00,6c,00,6c,00,00,00
	"ThreadingModel"="Both"

	[HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Classes\CLSID\{2FE8F810-B2A5-11d0-A787-0000F803ABFC}]
	@="DirectPlayLobby Object"

	[HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Classes\CLSID\{2FE8F810-B2A5-11d0-A787-0000F803ABFC}\InProcServer32]
	@=hex(2):25,00,53,00,79,00,73,00,74,00,65,00,6d,00,52,00,6f,00,6f,00,74,00,25,\
	  00,5c,00,53,00,79,00,73,00,57,00,6f,00,77,00,36,00,34,00,5c,00,64,00,70,00,\
	  6c,00,61,00,79,00,78,00,2e,00,64,00,6c,00,6c,00,00,00
	"ThreadingModel"="Both"

в разных версиях ОС (х86 х64) пути в реестре могу меняться.
	(пример)
	
	[HKEY_CLASSES_ROOT\DirectPlayLobby]
	@="DirectPlayLobby Object"

	[HKEY_CLASSES_ROOT\DirectPlayLobby\CLSID]
	@="{2FE8F810-B2A5-11d0-A787-0000F803ABFC}"
	
	
	
значение  @=hex(2): = путь до dplayx.dll (%SystemRoot%\SysWOW64\dplayx.dll)




