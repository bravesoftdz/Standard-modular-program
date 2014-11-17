Стандарт модульных программ
========================
Стандарт модульных программ - это стандарт обмена сообщениями между программами. Обмен сообщениями происходит при помощи сообщения Windows - WM_COPYDATA, на Linux - D-Bus?

<h2>Обмен сообщениями</h2>
Если нужно узнать статус программы, то мы отправляем ей сообщение "WORK" (чтобы найти handle для отправки, можно использовать, например, поиск по заголовку приложения ), после чего получаем ответ от нее в виде "YES&ID_HANDLE".
Перед отправкой сообщения "YES&ID_HANDLE" программа должна спросить пользователя о том, разрешать ли ей доступ в это приложение. Если пользователь разрешает ей доступ, то программа добавляет ее в список разрешенных. После чего 
происходит некоторое действие, в зависимости от команд приложения. После успешно выполненной команды приложения отправляет "GOOD", а в случае не успешного выполнения "BAD".


<h2>Пример работы</h2>
Программа 1 (Подкаст-менеджер) загружает подкасты.<br>
Программа 2 (Программа синхронизации смартфона).<br>

Программа 1 -> "WORK" -> Программа 2<br>
Программа 2 -> "YES" -> Программа 1<br>
Программа 1 -> "FILES TO SYNC\n...\n..." -> Программа 2<br>
Программа 2 -> "GOOD" -> Программа 1

*Программа 1 передала название файлов программе 2, программа 2 загрузила файлы на смартфон, отправила программе 1 статус успешной загрузки, программа 1 удалила загруженные файлы.


<h2>Реализация на Delphi 7 с обработкой WM_COPYDATA</h2>

Получение
<blockquote>...<br>
type<br>
  TForm1 = class(TForm)<br>
    Label1: TLabel;<br>
  private<br>
   procedure WMCopyData(var Msg: TWMCopyData); message WM_COPYDATA; //Наша функция<br>
...<br>
procedure TForm1.WMCopyData(var Msg: TWMCopyData);<br>
begin<br>
if copy(PChar(TWMCopyData(Msg).CopyDataStruct.lpData),1,4)='YES&' then ShowMessage('К нам пытается подключится программа');<br>
Msg.Result:=Integer(True);<br>
end;</blockquote>

Отправка
<blockquote>var<br>
CDS: TCopyDataStruct;<br>
begin<br>
CDS.dwData:=0;<br>
CDS.cbData:=(length('Команда')+ 1)*sizeof(char);<br>
CDS.lpData:=PChar('Команда');<br>
SendMessage(FindWindow(nil, 'Заголовок программы'),WM_COPYDATA, Integer(Handle), Integer(@CDS));<br>
end;</blockquote>
<br><br>
<h2>Standard modular programs</h2>

Standard modular programs (eng. Standard modular program) - a standard messaging between programs. Messages are exchanged using messages Windows - WM_COPYDATA, on Linux - D-Bus?

<h2>Messaging</h2>
If you want to know the status of the program, we will send a message to her "WORK" (to find the handle to send, can be used, for example, search for the application title), and then get the answer from her as a "YES & ID_HANDLE".
Before sending the message "YES & ID_HANDLE" program should ask the user about whether to allow her access to this application. If the user allows it access, the program adds it to the list of allowed. whereupon
there is some action depending on the application commands. After successful completion of the application sends a command "GOOD", and in case no successful implementation of "BAD".


<h2>Example of</h2>
Program 1 (Podcast Manager) download podcasts. <br>
Program 2 (Program smartphone sync). <br>

Program 1 -> "WORK" -> Program 2 <br>
Program 2 -> "YES" -> Program 1 <br>
Program 1 -> "FILES TO SYNC \ n ... \ n ..." -> Program 2 <br>
Program 2 -> "GOOD" -> Program 1

* Program 1 Program file name passed 2, program 2 Download the file to your smartphone, send the program status 1 successful download, the program 1 delete the downloaded file.


<h2>Implementation in Delphi 7 with processing WM_COPYDATA</h2>

Receiving
<blockquote>...<br>
type<br>
  TForm1 = class(TForm)<br>
    Label1: TLabel;<br>
  private<br>
   procedure WMCopyData(var Msg: TWMCopyData); message WM_COPYDATA; //Our function<br>
...<br>
procedure TForm1.WMCopyData(var Msg: TWMCopyData);<br>
begin<br>
if copy(PChar(TWMCopyData(Msg).CopyDataStruct.lpData),1,4)='YES&' then ShowMessage('We were trying to connect the program');<br>
Msg.Result:=Integer(True);<br>
end;</blockquote>

Shipping
<blockquote>var<br>
CDS: TCopyDataStruct;<br>
begin<br>
CDS.dwData:=0;<br>
CDS.cbData:=(length('Command')+ 1)*sizeof(char);<br>
CDS.lpData:=PChar('Command');<br>
SendMessage(FindWindow(nil, 'Title program'),WM_COPYDATA, Integer(Handle), Integer(@CDS));<br>
end;</blockquote>
