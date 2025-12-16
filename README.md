## Memory Management Simulation
A memory management system that allocates and deallocates memory for processes using fixed partitioning, paging, or segmentation.
Developing a memory management simulation is an excellent way to understand core OS concepts like memory allocation, fragmentation, and management techniques like partitioning, paging, and segmentation. Below is a step-by-step guide to building a simulation of a memory manager in Python.

### Project: Memory Management Simulation

We’ll simulate a memory management system that allocates and deallocates memory for processes using fixed partitioning, paging, or segmentation. This example will focus on fixed partitioning and paging.

---

### 1. Define the Basic Setup

We’ll start by defining the memory structure and the basic properties of processes that will request memory allocations.

#### Requirements:
- **Total Memory Size**: Define a fixed amount of memory (e.g., 1024 KB).
- **Memory Allocation Method**: Implement two types of memory management:
  - **Fixed Partitioning**: Divides memory into equal-sized partitions.
  - **Paging**: Divides memory into fixed-size pages and allocates pages as needed.

### 2. Implement Fixed Partitioning Memory Allocation

In fixed partitioning, memory is divided into equal-sized blocks, and each process must fit into one of these blocks.

#### Fixed Partitioning Code Example

```python
class MemoryManagerFixedPartitioning:
    def __init__(self, total_memory, partition_size):
        self.total_memory = total_memory
        self.partition_size = partition_size
        self.num_partitions = total_memory // partition_size
        self.memory = [None] * self.num_partitions  # None indicates free partition

    def allocate(self, process_id):
        for i in range(self.num_partitions):
            if self.memory[i] is None:  # Check for a free partition
                self.memory[i] = process_id
                print(f"Process {process_id} allocated at partition {i}.")
                return i
        print(f"No free partition available for Process {process_id}.")
        return -1

    def deallocate(self, process_id):
        for i in range(self.num_partitions):
            if self.memory[i] == process_id:
                self.memory[i] = None
                print(f"Process {process_id} deallocated from partition {i}.")
                return
        print(f"Process {process_id} not found in memory.")

    def display_memory(self):
        print("Memory partitions:", self.memory)

# Usage example
fixed_partition_manager = MemoryManagerFixedPartitioning(total_memory=1024, partition_size=256)
fixed_partition_manager.allocate("P1")
fixed_partition_manager.allocate("P2")
fixed_partition_manager.display_memory()
fixed_partition_manager.deallocate("P1")
fixed_partition_manager.display_memory()
```

In this code:
- **`allocate()`**: Searches for a free partition and assigns it to a process.
- **`deallocate()`**: Frees the partition occupied by a specific process.
- **`display_memory()`**: Shows the current allocation state of each partition.

---

### 3. Implement Paging Memory Allocation

Paging divides memory into fixed-size pages, and each process is allocated as many pages as needed. We simulate this by creating a table of pages, each of which can be assigned to a process.

#### Paging Code Example

```python
class MemoryManagerPaging:
    def __init__(self, total_memory, page_size):
        self.total_memory = total_memory
        self.page_size = page_size
        self.num_pages = total_memory // page_size
        self.memory = [None] * self.num_pages  # None indicates free page

    def allocate(self, process_id, process_size):
        num_pages_needed = -(-process_size // self.page_size)  # Ceiling division to get pages needed
        allocated_pages = []
        
        for i in range(self.num_pages):
            if len(allocated_pages) == num_pages_needed:
                break
            if self.memory[i] is None:  # Free page found
                self.memory[i] = process_id
                allocated_pages.append(i)

        if len(allocated_pages) == num_pages_needed:
            print(f"Process {process_id} allocated at pages {allocated_pages}.")
            return allocated_pages
        else:
            # Rollback if not enough pages are available
            for page in allocated_pages:
                self.memory[page] = None
            print(f"Not enough memory to allocate Process {process_id}.")
            return []

    def deallocate(self, process_id):
        for i in range(self.num_pages):
            if self.memory[i] == process_id:
                self.memory[i] = None
        print(f"Process {process_id} deallocated from memory.")

    def display_memory(self):
        print("Memory pages:", self.memory)

# Usage example
paging_manager = MemoryManagerPaging(total_memory=1024, page_size=128)
paging_manager.allocate("P1", 300)  # Process P1 needs 300KB
paging_manager.allocate("P2", 128)  # Process P2 needs 128KB
paging_manager.display_memory()
paging_manager.deallocate("P1")
paging_manager.display_memory()
```

In this code:
- **`allocate()`**: Calculates the number of pages needed based on process size. Allocates pages if available; otherwise, rolls back the allocation.
- **`deallocate()`**: Frees pages associated with a process.
- **`display_memory()`**: Shows the current page allocation status.

---

### 4. Simulating Fragmentation

To simulate and observe fragmentation:
- **External Fragmentation** (for fixed partitioning): Occurs when there’s enough total memory, but it’s divided into chunks too small for the requesting process.
- **Internal Fragmentation** (for paging): Happens when a process doesn’t fully use a page, leading to wasted space within the allocated pages.

Try running allocation and deallocation in random order to observe how these forms of fragmentation might occur.

---

### 5. Further Extensions

To expand the project, consider:
- **Segmentation**: Implement a segmentation-based memory manager, where each process can request different segment sizes.
- **Defragmentation**: Add a defragmentation method to compact memory in case of excessive fragmentation.
- **Visualization**: Visualize the memory structure in a grid or chart to show partition/page allocation over time.

---

### Summary

This project gives a hands-on understanding of:
- **Fixed Partitioning** and **Paging**
- **Fragmentation** issues and their impact on memory allocation
- **Memory management techniques** and their trade-offs

This foundational understanding is directly applicable to how real operating systems manage memory allocation, and it helps you gain a more intuitive grasp of OS-level memory handling.
