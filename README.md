## A* Algorithm for Missionary and Cannibals Problem(a스타 알고리즘을 사용한 선교사 문제)
3명의 선교사와 3명의 식인종이 배를 타고 강을 건너려고 한다. 배에는 2명 밖에 탈수없다. 만일 강의 어느 쪽에서라도 식인종의 수가 선교사의 수가 많다면 식인종은 선교사를 잡아먹게 된다. 이때 선교사는 식인종에게 잡아먹히지않고 강을 건널려먼 어떻게 해야 하는가? <br><br>
Three missionaries and three cannibals try to cross the river by boat. Only two people can ride a boat. If there is a large number of cannibals on either side of the river, the cannibals will eat missionaries. What should the missionary do when crossing the river without being eaten by cannibals?<br><br>

### How to solve
해당 프로그램을 git clone을 통해 다운로드 해줍니다.
```
$git clone https://github.com/chanhee-kang/a-algorithm_missionary.git
```

### 실행
start.py 파일을 열어 주시면 아래의 코드가 열립니다
```
from queue import PriorityQueue

class MCNode:
  def __init__(self, m, c, b):
    self.missionaries = m
    self.cannibals = c
    self.boatpos = b
    self.h = (m * m + c * c + 2 * m * c)
    self.predecessor = None

  def __eq__(self, other):
    return other.boatpos == self.boatpos and \
           other.missionaries == self.missionaries and \
           other.cannibals == self.cannibals

  def getAdjacentNodes(self, m, c, bc):
    adj_nodes = []
    carry = 1
    while carry <= bc:
      carrym = carry
      carryc = 0
      while carrym >= 0:
        if self.boatpos == 'L':
          newm = self.missionaries - carrym
          newc = self.cannibals - carryc
          if newm >= 0 and newc >= 0 and (newm >= newc or newm == 0) and ((m - newm) >= (c - newc) or (m - newm == 0)):
            adj_nodes.append(MCNode(newm, newc, 'R'))
        elif self.boatpos == 'R':
          newm = self.missionaries + carrym
          newc = self.cannibals + carryc
          if newm <= m and newc <= c and (newm >= newc or newm == 0) and ((m - newm) >= (c - newc) or (m - newm == 0)):
            adj_nodes.add(MCNode(newm, newc, 'L'))
        carrym -= 1
        carryc += 1
      carry += 1
    return adj_nodes

  def printPath(self):
    if self.predecessor is not None:
      self.predecessor.printPath()
    print("<" + self.missionaries + "," + self.cannibals + "," + self.boatpos + "> ")


class MCGraph:
  def __init__(self):
    self.vertex_list = []
    self.open_list = []
    self.closed_list = []
    self.maxm = 0
    self.maxc = 0
    self.boatcarry = 0

  def astarSearch(self, m, c, bc):
    siddu_steps = 0
    self.maxm = m
    self.maxc = c
    self.boatcarry = bc
    self.open_list = PriorityQueue()
    self.closed_list = set()
    start = MCNode(m, c, 'L')
    start.g = 0
    self.open_list.put((start.h + start.g, start))
    transfer_node = None
    while ( not self.open_list.empty()) and (transfer_node is None or transfer_node.missionaries != 0 or transfer_node.cannibals != 0 or transfer_node.boatpos != 'R'):
      siddu_steps += 1
      transfer_node = self.open_list.get()
      self.closed_list.add(transfer_node)
      adj_nodes = transfer_node.getAdjacentNodes(m, c, self.boatcarry)
      for adj_node in adj_nodes:
        inCL = False
        for closed_node in self.closed_list:
          if adj_node == closed_node:
            if (transfer_node.g + 1 < closed_node.g):
              closed_node.g = transfer_node.g + 1
              closed_node.predecessor = transfer_node
              self.parentRedirection(closed_node)
            inCL = True
            break
        if (inCL):
          continue
        inOL = False
        for open_node in self.open_list:
          if (adj_node == open_node):
            if (transfer_node.g + 1 < open_node.g):
              open_node.g = transfer_node.g + 1
              open_node.predecessor = transfer_node
            inOL = True
            break
        if (inOL):
          continue
        adj_node.g = transfer_node.g + 1
        adj_node.predecessor = transfer_node
        self.open_list.put((adj_node.h + adj_node.g, adj_node))

    print(siddu_steps)
    if transfer_node.missionaries == 0 and transfer_node.cannibals == 0 and transfer_node.boatpos == 'R':
      print("Path:")
      transfer_node.printPath()
  def parentRedirection(self, node):
    adj_nodes = node.getAdjacentNodes(self.maxm, self.maxc, self.boatcarry)
    for adj_node in adj_nodes:
      for closed_node in self.closed_list:
        if adj_node == closed_node:
          if node.g + 1 < closed_node.g:
            closed_node.g = node.g + 1
            closed_node.predecessor = node
            self.parentRedirection(closed_node)
          break
```

### Contact
If you have any requests, please contact: [https://ck992.github.io/](https://ck992.github.io/).

