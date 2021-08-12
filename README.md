## Queue

An optimized thread-safe atomic queue written using C++20, giant performance with no additional push/poll wait CPU consumption for synchronizing data between threads.

```cpp
template<class T> class queue(capacity) // capacity default to UINT16_MAX = 65535
```

- `void push(const T& item)` - Enqueue an item by reference, blocks if queue is full, unblock after an item dequeued.

- `void push(T&& item)` - Enqueue an item using Rvalue copy construction, blocks if queue is full, unblock after an item dequeued.

- `T pop()` - Dequeue and move an item out, blocks if queue is empty, unblock after an item enqueued.

There is no additional push/poll wait CPU consumption that the push or poll threads can block or wake-up each, and the congestible feature made it useful in synchronizing not only data but also threads.

Note that in some use cases, the container inside the queue would change the items lifetime, so that use this queue to synchronize some type of item(s) may not work as excepted.

<details>
    <summary>Click to view examples 👇</summary>

```cpp
#include <thread>
#include <ranges>
#include <iostream>

#include "include/queue.hpp"

int main() {
    ubn::queue<int> q;
    auto t[] = {
        std::jthread([&]() {
            for (const auto& i : std::views::iota(0, 10))
                std::cout << "thr1: pop <- " << q.pop() << "\n";
        }),
        std::jthread([&]() {
            for (const auto& i : std::views::iota(0, 10))
                std::cout << "thr2: pop <- " << q.pop() << "\n";
        }),
        std::jthread([&]() {
            for (const auto& i : std::views::iota(0, 10)) {
                std::cout << "thr3: push -> " << i << "\n";
                q.push(i);
            }
        }),
        std::jthread([&]() {
            for (const auto& i : std::views::iota(0, 10)) {
                std::cout << "thr4: push -> " << i << "\n";
                q.push(i);
            }
        })
    };
}
```
</details>
