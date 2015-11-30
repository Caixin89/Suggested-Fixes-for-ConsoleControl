# Suggested-Fixes-for-ConsoleControl
Suggested Fixes for ConsoleControl located at https://github.com/dwmkerr/consolecontrol

---ConsoleControl.WPF---
1. In constructor of ConsoleControl, richTextBoxConsole.KeyDown += richTextBoxConsole_KeyDown should be changed to richTextBoxConsole.PreviewKeyDown += richTextBoxConsole_KeyDown. This is because KeyDown event do not capture Enter key.

2. In WriteOutput(string output, Color color), inputStartPos = richTextBoxConsole.Document.ContentEnd should be changed to inputStartPos = richTextBoxConsole.Document.ContentEnd.GetPositionAtOffset(-2) to account for \r\n. This is because user input will start before the \r\n.

3. In StartProcess(string fileName, string arguments), 
    if (IsInputEnabled)
      richTextBoxConsole.IsReadOnly = false; 
  should be changed to 
    if (IsInputEnabled)
      richTextBoxConsole.IsReadOnly = false;
    else
      richTextBoxConsole.IsReadOnly = true;
  because default value of RichTextBox.IsReadOnly is false so you need to set to true if IsInputEnabled is false.

4. I maintain a StringBuilder variable instead of using inputStartPos to get the current input string once user enters Return key. This is because inputStartPos seems to update itself everytime you enter an input, causing var input = new TextRange(inputStartPos, richTextBoxConsole.Selection.Start).Text to always return "".
5. 

---ProcessInterface.cs in ConsoleControlAPI---
1. Change to create new instance of outputWorker and errorWorker everytime StartProcess is called. If not, calling StartProcess after a StopProcess will cause a crash.
