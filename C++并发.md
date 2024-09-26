无锁编程



CAS

```C++
// 返回 reg 的值
// 如果 reg 等于旧值，设置为新值
int compare_and_swap (int* reg, int oldval, int newval)
{
  int old_reg_val = *reg;
  if (old_reg_val == oldval)
     *reg = newval;
  return old_reg_val;
}
```

C++ 的 CAS

```c++
// 如果原子变量和 expected 相等，将其赋值为 desired，返回 true 否则 expected 赋值为当前值并返回 false
bool std::atomic<T>::compare_exchange_weak(T &expected, T desired);
bool std::atomic<T>::compare_exchange_strong(T &expected, T desired);

// 相当于
template <typename T>
bool atomic<T>::compare_exchange_strong(T &expected, T desired) {
    std::lock_guard<std::mutex> guard(m_lock);
    if (m_val == expected)
        return m_val = desired, true;
    else
        return expected = m_val, false;
}
```

`compare_exchange_weak` 和 `compare_exchange_strong` 的区别在于, `compare_exchange_weak` 有可能在当前值与 `expected` 相等时仍然不执行交换并返回 `false`; `compare_exchange_strong` 则不会有这个问题. weak 版本能让编译器在一些平台下生成一些更优的代码, 在 x86 下是没区别的.



