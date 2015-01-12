<h2>RU:</h2>
Стандарт модульных программ - это концепция <del>стандарт</del> обмена сообщениями между программами. Обмен сообщениями происходит при помощи сообщения Windows - WM_COPYDATA, на Linux - D-Bus?

<b>Обмен сообщениями</b>

Для того, чтобы узнать статус программы, мы отправляем ей сообщение "WORK" (чтобы найти handle для отправки, можно использовать поиск по заголовку приложения ), после чего получаем ответ в виде "YES&ID_HANDLE".
Перед отправкой сообщения "YES&ID_HANDLE" программа должна спросить пользователя о том, разрешать ли ей доступ в это приложение. Если пользователь разрешает ей доступ, то программа добавляет ее в список разрешенных. После чего 
происходит некоторое действие, в зависимости от команд приложения. После успешно выполненной команды приложения отправляет "GOOD", а в случае не успешного выполнения "BAD".


<b>Пример работы</b>

Программа 1 (Подкаст-менеджер) загружает подкасты.<br>
Программа 2 (Программа синхронизации смартфона).<br>

Программа 1 -> "WORK" -> Программа 2<br>
Программа 2 -> "YES" -> Программа 1<br>
Программа 1 -> "FILES TO SYNC\n...\n..." -> Программа 2<br>
Программа 2 -> "GOOD" -> Программа 1

*Программа 1 передала название файлов программе 2, программа 2 загрузила файлы на смартфон, отправила программе 1 статус успешной загрузки, программа 1 удалила загруженные файлы.


<b>Реализация на Delphi 7 с обработкой WM_COPYDATA</b>

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
<br>
<h2>EN:</h2>
Standard modular programs (eng. Standard modular program) - a concept <del>standard</del> messaging between programs. Messages are exchanged using messages Windows - WM_COPYDATA, on Linux - D-Bus?

<b>Messaging</b>

To find out the status of the program, we send her a message "WORK" (to find the handle to send, you can search for the title of the application), and then get an answer in the form of "YES & ID_HANDLE".
Before sending the message "YES & ID_HANDLE" program should ask the user about whether to allow it access to this application. If the user allows access to it, the program adds it to the list of allowed. whereupon
takes some action depending on the application commands. After successful completion of the application sends a command "GOOD", and in case of successful implementation of "BAD".

<b>Example</b>

Program 1 (Podcast Manager) download podcasts. <br>
Program 2 (Program smartphone sync). <br>

Program 1 -> "WORK" -> Program 2 <br>
Program 2 -> "YES" -> Program 1 <br>
Program 1 -> "FILES TO SYNC \ n ... \ n ..." -> Program 2 <br>
Program 2 -> "GOOD" -> Program 1

* Program 1 Program file name passed 2, program 2 Download the file to your smartphone, send the program status 1 successful download, the program 1 delete the downloaded file.


<b>Implementation in Delphi 7 with processing WM_COPYDATA</b>

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
