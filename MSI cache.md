# MSI Protocol

## There are three status in MSI -- Modified, Shared, Invalid
### Status:
1. Invalid Status: I.
2. Shared Status: S, means this data not modified in the cache, and it is latest in the main memory. Other caches might or not contain the copy of this data.
3. Modified Status: M, means only this cache contains the latest version of this data, the data in the main memory is outdated, other caches do not contain the valid copy of this data.

### Event:
1. Events of processor: Processor Read (PrRd), Processor Write (PrWr).
2. Events of bus: Bus Read (BusRd), Bus Mutex Read (BusRdx), Bus Write Back (BusWB).
	1. BusRd: Cache controller puts address on bus, asks for a data block, main memory or cache will provide the data.
	2. BusRdx: Ask for the data block, the aim is to modify this data block, and this data block is exclusive, other caches cannot access it.
	3. BusWB: Cache writes data block back into the main memory.

To support events above, cache controller provides a new event: FLUSH, the aim is to response the events on the bus, and put the asked data block directly from cache to bus, isntead of transiting the data via the main memory.

![](https://courses.cs.washington.edu/courses/csep524/99wi/lectures/lecture5/img015.JPG)

### Invalid Status:
1. When PrRd happens, it will result cache miss, and cache will store the latest data. BusRd will happen to help cache store the latest data. Then it becomes S statues.
2. When PrWr happens, it will result in cache miss, and it will ask for BusRdx to store the data and modify the data. Then it becomes M status.

### Shared Status:
1. When PrRd happens, since the data already stored in the cache, so it will result in cache hit, it will keep S status.
2. When PrWr happens, it will ask BusRdx, after it modifies the data, it becomes M status.
3. When receive the signal of BusRd from Bus, it knows other cache is reading this data, nothing to do with itself, so it keeps S status.
4. When receive the signal of BusRdx from Bus, it knows other cache will modify this data, so it will Flush first, then becomes I status.

### Modified Status:
1. When PrRd happens, since this data is already the latest, it will result in cache hit, and no influence on other caches, it keeps M status.
2. When PrWr happens, since this data is already the latest, after the modification, and no influence on other caches, it keeps M status.
3. When receive the signal of BusRd, since it contains the latest data, it will Flush, and becomes S status.
4. Whenreceive the signal of BusRdx, it knows other cache will modify this data, it will Flush and becomes I status.

## Reference: 
Violet-Guo (https://blog.csdn.net/violet_echo_0908/article/details/78839692)
