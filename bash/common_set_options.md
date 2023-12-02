# 常用 `set` 选项

## `set -e`：出错后停止执行

> Exit immediately if a pipeline (see Pipelines), which may consist of a single simple command (see Simple Commands), a list (see Lists of Commands), or a compound command (see Compound Commands) returns a non-zero status.

当 bash 脚本遇到不等于 0 的返回值时，直接退出。

## `set -u`：不允许未定义的变量

> Treat unset variables and parameters other than the special parameters ‘@’ or ‘\*’, or array variables subscripted with ‘@’ or ‘\*’, as an error when performing parameter expansion. An error message will be written to the standard error, and a non-interactive shell will exit.

当发现脚本中有未定义变量时，退出脚本。

## `set -x`：显示执行的命令

> Print a trace of simple commands, for commands, case commands, select commands, and arithmetic for commands and their arguments or associated word lists after they are expanded and before they are executed. The value of the PS4 variable is expanded and the resultant value is printed before the command and its expanded arguments.

打印出每条执行的命令，展开变量。

**Reference**: [The Set Builtin (Bash Reference Manual)](https://www.gnu.org/software/bash/manual/html_node/The-Set-Builtin.html)
