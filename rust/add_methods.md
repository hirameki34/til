# 整数加法方法

Rust 中整数运算包含不同方法，处理溢出和特定需求：

1. **`wrapping_add`**: 溢出时环绕（模运算），返回结果。适合计数器、哈希函数。
   - 示例: `255u8.wrapping_add(1) == 0`

2. **`overflowing_add`**: 溢出时环绕，返回 `(结果, 是否溢出)` 元组。适合检查溢出。
   - 示例: `255u8.overflowing_add(1) == (0, true)`

3. **`saturating_add`**: 溢出时饱和到最大/最小值，返回结果。适合图像、音频处理。
   - 示例: `255u8.saturating_add(1) == 255`

4. **`checked_add`**: 溢出时返回 `None`，否则返回 `Some(结果)`。适合数值安全。
   - 示例: `255u8.checked_add(1) == None`

5. **`carrying_add`**: 溢出时环绕，返回 `(结果, 进位)` 元组。适合大整数、加密算法。
   - 示例: `255u8.carrying_add(1, false) == (0, true)`

6. **`unchecked_add`**: `unsafe`，溢出未定义。

7. **默认 `+`**: Debug 模式下溢出 panic，Release 模式下 wrapping。
