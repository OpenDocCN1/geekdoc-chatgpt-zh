# 多路降序数组的Top K，堆一次搞定
想从几条已经排好序的行里一次性挑出前K个最大数，最省力的办法就是用一个小根堆做多路归并。先把每行的第一个数丢进堆，用负号把小根堆变成大根堆的视角，每次弹出当前最大，再把该行下一个数补回去，重复K次就能得到结果。下面是可直接运行的Python实现。

[PRE0]
import heapq

def getTop(data):
    rowCount = len(data)
    colCount = len(data[0])
    topK = [None] * colCount

    # 用最小堆保存各行的当前候选，通过取负值实现最大值优先
    heap = []
    idx = 0
    while idx < rowCount:
        node = (-data[idx][0], idx, 0)
        heapq.heappush(heap, node)
        idx += 1

    pos = 0
    while pos < colCount:
        cur = heapq.heappop(heap)
        topK[pos] = -cur[0]
        pos += 1
        if pos >= colCount:
            break
        nxt = (-data[cur[1]][cur[2] + 1], cur[1], cur[2] + 1)
        heapq.heappush(heap, nxt)

    return topK

if __name__ == "__main__":
    data = [[29, 17, 14, 2, 1], [19, 17, 16, 15, 6], [30, 25, 20, 14, 5]]
    print getTop(data)
[PRE1]
运行后会得到 [30, 29, 25, 20, 19]，正是三行各自降序排列后整体前五大的结果。时间复杂度由堆操作主导，空间只用 O 行数。