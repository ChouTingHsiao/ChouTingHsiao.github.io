---
title: 如何運行 Shell 命令
date: 2022-05-22 00:00:00
categories: .Net
tags: [.Net, Shell]
---

# 引用參考

```csharp
using System.Diagnostics;
```

<!--more-->

# 運行 Shell 命令

透過 Process 執行命令
```csharp
string cmd = "echo Hello, World!";

// linux 使用 bash
string fileName = "/bin/bash";
string argument = "-c";

bool isWin = Environment.OSVersion.Platform.ToString().Contains("Win");

if(isWin)
{
    // windows 使用 cmd
    fileName = "cmd.exe";
    argument = "/c";
}

string escapedArgs = cmd.Replace("\"", "\\\"");

using (Process process = new Process())
{
    process.StartInfo = new ProcessStartInfo
    {
        FileName = "cmd.exe",
        Arguments = $"{argument} \"{escapedArgs}\"",
        RedirectStandardOutput = true,
        RedirectStandardError = true,
        UseShellExecute = false,
        CreateNoWindow = true,
    };

    process.Start();

    string result = process.StandardOutput.ReadToEnd();

    Console.WriteLine(result);

    string error = process.StandardError.ReadToEnd();

    Console.WriteLine(error);

    process.WaitForExit();
}
```

{% note warning %}
📜 參考資料
1. [Process 類別](https://learn.microsoft.com/zh-tw/dotnet/api/system.diagnostics.process?view=net-8.0)
{% endnote %}
