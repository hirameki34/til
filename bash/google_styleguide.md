# Google Shell 风格指南

可执行的脚本应以 `#!/bin/bash` 开始。

---

所有错误应当输出到 `STDERR`, 建议使用函数打印错误信息和其他状态信息。

```bash
err() {
  echo "[$(date +'%Y-%m-%dT%H:%M:%S%z')]: $*" >&2
}

if ! do_something; then
  err "Unable to do_something"
  exit 1
fi
```

---

对于超过一个函数的脚本，需要 `main` 函数，帮助快速定位程序入口。对于短脚本，不需要 `main`。

```bash
main "$@"
```

---

始终检查返回值。

``` bash
if ! mv "${file_list[@]}" "${dest_dir}/"; then
  echo "Unable to move ${file_list[*]} to ${dest_dir}" >&2
  exit 1
fi

# Or
mv "${file_list[@]}" "${dest_dir}/"
if (( $? != 0 )); then
  echo "Unable to move ${file_list[*]} to ${dest_dir}" >&2
  exit 1
fi
```

对于管道命令，使用 `PIPESTATUS` 变量。

```bash
tar -cf - ./* | ( cd "${dir}" && tar -xf - )
if (( PIPESTATUS[0] != 0 || PIPESTATUS[1] != 0 )); then
  echo "Unable to tar files to ${dir}" >&2
fi
```

注意 `PIPESTATUS` 会被其他命令 (如 `[`) 重置。

```bash
tar -cf - ./* | ( cd "${DIR}" && tar -xf - )
return_codes=( "${PIPESTATUS[@]}" )
if (( return_codes[0] != 0 )); then
  do_something
fi
if (( return_codes[1] != 0 )); then
  do_something_else
fi``bash

```

---

**Reference** [Shell Style Guide](https://google.github.io/styleguide/shellguide.html)
