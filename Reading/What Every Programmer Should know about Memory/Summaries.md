![ChaptersReviewed](assets/20251113_125356_Screenshot.png)

### Glossary

DMA: Direct Memory Access

NUMA: Non-Uniform Memory Access

SRAM: Static Random Access Memory: Fast volatile computer memory that uses bistable latching circuitry to store each bit of data. Unlike DRAM, it doesn't need to be refreshed as long as power is supplied, which means its super fast but also more expensive and less dense. 

DRAM: Dynamic Random-Access Memory: A type of volatile memory that serves as a computers main working memory, storing data temporarily for active programs and the CPU. Because it uses a grid of memory cells, each made of a transistor and a capacitor, to store a single bit of data, it needs to be constantly and periodically refreshed. 

Volatile Memory: a type of computer memory that loses its data when power is turned off, requiring a continuous power supply to maintain information.

Temporal Locality: Temporal locality is the principle that if a memory location is referenced at one point in time, it is likely to be referenced again in the near future.

Spatial Locality: Spatial locality is the principle that a program is likely to access memory locations near a location that was recently accessed.

## Chapter 1: Introduction

As computers got more complex, we went from shared memory computers to computers with individual parts. When it was shared, every part on the computer was fast, but since then we wanted to optimize individual subsystems such as the CPU. Disk and other components suffered from this change, creating bottlenecks. As a way to improve speed and memory usage after the splitting of components, we implement changes in the following forms

- RAM hardware design (Speed and Parallelism)
- Memory Controller Designs
- CPU Caches
- Direct Memory Access (DMA) for devices.

in order to mitigate the new found bottlenecks of systems. 

So in result, this paper looks into CPU Caching, effects of memory controller design, DMA, and how commodity hardwares is desgined and how our memory and subsystems are affected. Aswell we look into RAM And why these differences still exist.



## Chapter 3: CPU Caches

#### 3.0 CPU Caches

CPUs went from having equivalent speed to their RAM Counterpart, where memory access was only a bit slower than register access, to now the CPU register access is leagues faster than RAM access. As CPU speed increased, RAM did too, but creating RAM that matched the CPU was never economically viable.

What this meant is that in order to unlock the CPU's speed without killing our bank accounts, we decided to have alot of good RAM, rather than a small amount of ultra fast RAM, with secondary storage becoming the bottleneck. This meant choosing between a tiny amount of ultra-fast RAM or large amounts of good RAM. For workloads requiring substantial data, the larger amount wins: despite being slower, it avoids constantly swapping to secondary storage (hard disks), which is orders of magnitude slower still.

SRAM is the faster, more expensive RAM, with DRAM being its counterpart. Managing SRAM is diffcult at the application level, and would cause a loss of the gains since managing access would be expensive. So in order to use SRAM properly, we assigned it to the CPU. 

What this meant is that the CPU can take advantage of the SRAM whenever it wants. It would use the SRAM to make temporary copies (to cache) data in main memory which would likely be used soon because of locality principles. 





