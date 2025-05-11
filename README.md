## 🧵 Atomic Locking Using `amoswap.w` (RISC-V)

This example demonstrates how to implement a basic **mutex lock** using the RISC-V atomic instruction `amoswap.w`. This approach ensures **mutual exclusion** at the hardware level, and is often used in low-level systems such as embedded operating systems or bare-metal firmware where no OS primitives are available.

### 💡 Why Atomic Instructions?

In a multi-core system, race conditions can occur when two processors try to update the same memory variable at the same time. Atomic instructions like `amoswap.w` prevent this by **combining read, modify, and write into one uninterruptible operation**.

This ensures that only **one core or thread** can successfully claim a lock at a time, providing the foundation for **mutexes**, **spinlocks**, and **semaphores** in concurrent systems.

---

### 🔐 Lock and Unlock Example (Spinlock)

```assembly
# === LOCK ===
lock:
  li t0, 0x1000          # Address of the lock variable
  li t1, 1               # Value to swap in (1 = locked)

try_again:
  amoswap.w t2, t1, (t0) # Atomically swap t1 into memory, get old value in t2
  bnez t2, try_again     # If old value ≠ 0, someone else holds the lock → retry
  ret

# === UNLOCK ===
unlock:
  li t0, 0x1000          # Lock address
  li t1, 0               # Value to release lock (0 = unlocked)
  amoswap.w zero, t1, (t0) # Atomically store 0, discard old value
  ret
