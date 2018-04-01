# shareTex

环境为textlive+vscode+latex-workshop插件+git。

## vscode

安装后将该文件夹设置为工作路径，并设置快捷键。快捷键设置在`文件` -> `首选项` -> `键盘快捷方式`中，建议修改keybindings.json为如下：

```json
// 将键绑定放入此文件中以覆盖默认值
[
    {
        "key": "ctrl+L ctrl+L",
        "command": "workbench.action.tasks.runTask",
        "args":"sync",
        "when": "editorTextFocus"
    }
]
//for linux:
//"args":"synclinux"
```

所用args为定义在.vscode文件夹下的tasks.json中的一个任务，windows下label为`sync`，linux下应为`synclinux`。tasks.json的内容如下：

```json
{
    "_comment": "See https://go.microsoft.com/fwlink/?LinkId=733558",
    "_comment": "for the documentation about the tasks.json format",
    "version": "2.0.0",
    "tasks": [
        {
            "label": "sync",
            "type": "shell",
            "command": "git add -A;if ($?){git commit -m 'change text'};$commitFlag=$?;git pull --no-edit ;if ($? -and $commitFlag) {git push}",
            "presentation":{
                "echo": true,
                "reveal": "never",
                "focus": false,
                "panel": "shared"
            }
        },
        {
            "label": "synclinux",
            "type": "shell",
            "command": "git add -A && git commit -m 'change text' ; commitFlag=$? ; git pull --no-edit && if [ $commitFlag -eq 0 ]; then git push; fi ",
            "presentation":{
                "echo": true,
                "reveal": "never",
                "focus": false,
                "panel": "shared"
            }
        }
    ]
}
```

## latex-workshop 插件

该插件在保存文件时会自动编译。使用的编译程序为latexmk，能够比较好的判断所需要编译的次数。若需要使用xelatex编译，并且仍然保持自动判断编译次数的特性，可在用户设置中将latex-workshop的` "latex-workshop.latex.magic.args"`设置项改为如下：

```json
"latex-workshop.latex.magic.args": [
        "-xelatex",
        "-synctex=1",
        "-interaction=nonstopmode",
        "-file-line-error",
        "%DOC%"
    ],
```

设置后在.tex文件首行加入`%! TEX program = latexmk`即为使用xelatex编译，否则为使用默认的编译方式。

> 注意：xelatex的latexmk支持在texlive的2015的某个版本才开始支持。。。

## 最后

使用上还是有点小瑕疵，比如运行同步任务时最好不要修改文件，否则会提示内存和磁盘文件不同。另外就是编译是在pull下来之前进行的，所以同步任务运行时所编译生成的pdf是本地刚刚修改所生成的，而不是同步之后的.tex生成的。如果远程端内容有变化，可能得`Ctrl+Alt+B`手动再编译一次。但是，我觉得已经够用了。嗯。
