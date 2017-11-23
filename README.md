# RU:
Стандарт модульных программ - это концепция <del>стандарт</del> обмена сообщениями между программами. Обмен сообщениями происходит при помощи сообщения Windows - WM_COPYDATA.


### Обмен сообщениями

1. Ищем handle приложений по заголовку или иным способом.
2. Отправляем приложению сообщение "WORK" для получения статуса работы стандарта. Приложение спрашивает пользователя о добавлении программы отправителя в список разрешенных. В случае положительного ответа отправляем "YES", в ином случае "NO".
3. Если получен ответ "Yes", то происходит некоторое действие, в зависимости от команд приложения. После успешно выполненной команды приложения отправляет "GOOD", а в случае не успешного выполнения "BAD".


### Пример работы

Программа 1 (Подкаст-менеджер) загружает подкасты.
Программа 2 (Программа синхронизации смартфона).


Программа 1 -> "WORK" -> Программа 2<br>
Программа 2 -> "YES" -> Программа 1<br>
Программа 1 -> "FILES TO SYNC\n...\n..." -> Программа 2<br>
Программа 2 -> "GOOD" -> Программа 1

*Подкаст-менеджер передал названия загруженных файлов программе синхронизации смартфона, программа синхронизации смартфона загрузила файлы на смартфон и отправила статус успешной загрузки, подкаст-менеджер удалил загруженные файлы.*


#### Пример на Delphi 7 с обработкой WM_COPYDATA
##### Получение
<blockquote>...<br>
  private<br>
   procedure WMCopyData(var Msg: TWMCopyData); message WM_COPYDATA; //Наша функция<br>
...<br>
procedure TForm1.WMCopyData(var Msg: TWMCopyData);<br>
begin<br>
if copy(PChar(TWMCopyData(Msg).CopyDataStruct.lpData),1,4)='YES&' then ShowMessage('К нам пытается подключится программа');<br>
Msg.Result:=Integer(True);<br>
end;</blockquote>

##### Отправка
<blockquote>var<br>
CDS: TCopyDataStruct;<br>
begin<br>
CDS.dwData:=0;<br>
CDS.cbData:=(length('Команда')+ 1)*sizeof(char);<br>
CDS.lpData:=PChar('Команда');<br>
SendMessage(FindWindow(nil, 'Заголовок программы'),WM_COPYDATA, Integer(Handle), Integer(@CDS));<br>
end;</blockquote>


# EN:
Standard modular programs (eng. Standard modular program) - a concept <del>standard</del> messaging between programs. Messages are exchanged using messages Windows - WM_COPYDATA.

### Message exchange
1. Looking for handle applications by title or otherwise.
2. We send the message "WORK" to the application to get the status of the standard. The application asks the user if the sender program is added to the list of allowed programs. If yes, send "YES", otherwise "NO".
3. If the answer is "Yes", then some action takes place, depending on the application commands. After successfully executing the command, the application sends "GOOD", and in case of unsuccessful execution of "BAD".

### Example</b><br>
Program 1 (Podcast Manager) download podcasts. <br>
Program 2 (Program smartphone sync). <br>

Program 1 -> "WORK" -> Program 2 <br>
Program 2 -> "YES" -> Program 1 <br>
Program 1 -> "FILES TO SYNC \ n ... \ n ..." -> Program 2 <br>
Program 2 -> "GOOD" -> Program 1

*The podcast manager sended file names of the downloaded files to the smartphone synchronization program, the smartphone synchronization program downloaded the files to the smartphone and sent the status of a successful download, the podcast manager deleted the downloaded files.*


#### Example in Delphi 7 with processing WM_COPYDATA
##### Receiving
<blockquote>...<br>
  private<br>
   procedure WMCopyData(var Msg: TWMCopyData); message WM_COPYDATA; //Our function<br>
...<br>
procedure TForm1.WMCopyData(var Msg: TWMCopyData);<br>
begin<br>
if copy(PChar(TWMCopyData(Msg).CopyDataStruct.lpData),1,4)='YES&' then ShowMessage('We were trying to connect the program');<br>
Msg.Result:=Integer(True);<br>
end;</blockquote>

##### Sending
<blockquote>var<br>
CDS: TCopyDataStruct;<br>
begin<br>
CDS.dwData:=0;<br>
CDS.cbData:=(length('Command')+ 1)*sizeof(char);<br>
CDS.lpData:=PChar('Command');<br>
SendMessage(FindWindow(nil, 'Title program'),WM_COPYDATA, Integer(Handle), Integer(@CDS));<br>
end;</blockquote>
