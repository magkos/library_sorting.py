# library_sorting.py
import sys
import json
import bisect


class SparseTable:
    def __init__(self, n_list, m_list, k, x):
        self.n_list = n_list
        self.m_list = m_list
        self.k = k
        self.authentic_keys = [x]
        self.build_table()

  def build_table(self):
        n = len(self.authentic_keys)
        n_k_prev = self.n_list[self.k - 1]
        m_k = self.m_list[self.k]
        self.m = int(n_k_prev * m_k)
        self.table = [self.authentic_keys[0]] * self.m
        self.head = 0
        self.rebuild()

  def rebuild(self):
        # Αναδομή του πίνακα με ισοκατανομή αυθεντικών κλειδιών
        self.table = [None] * self.m
        spacing = self.m // len(self.authentic_keys)
        for i, key in enumerate(sorted(self.authentic_keys)):
            index = (i * spacing) % self.m
            self.table[index] = key
        # Fill gaps with dummy keys
        last_value = None
        for i in range(self.m):
            if self.table[i] is not None:
                last_value = self.table[i]
            else:
                self.table[i] = last_value
        self.head = sorted((i for i in range(self.m) if self.table[i] == self.authentic_keys[0]))[0]

  def display(self):
        result = []
        for i in range(self.m):
            real_i = (self.head + i) % self.m
            val = self.table[real_i]
            if i == 0:
                result.append(f'>{val}<')
            else:
                result.append(str(val))
        print(f"[{', '.join(result)}]")

  def insert(self, x):
        if x in self.authentic_keys:
            self.display()
            return

  if len(self.authentic_keys) == self.n_list[self.k]:
            # Rebuild to next k
            self.k += 1
            self.authentic_keys.append(x)
            self.build_table()
        else:
            self.authentic_keys.append(x)
            self.rebuild()
        self.display()

  def lookup(self, x):
        for i in range(self.m):
            idx = (self.head + i) % self.m
            if self.table[idx] == x:
                print(f"Key {x} found at position {i}.")
                self.display()
                return
            elif self.table[idx] > x:
                print(f"Key {x} not found. It should be at position {i}.")
                self.display()
                return
        print(f"Key {x} not found. It should be at position {self.m}.")
        self.display()

  def delete(self, x):
        if x not in self.authentic_keys:
            self.display()
            return
        self.authentic_keys = [k for k in self.authentic_keys if k != x]
        if len(self.authentic_keys) < self.n_list[self.k - 1] and self.k > 1:
            self.k -= 1
        self.build_table()
        self.display()

def main():
    if len(sys.argv) != 2:
        print("Usage: python library_sorting.py input.json")
        return

  with open(sys.argv[1]) as f:
        data = json.load(f)

  nn = data["nn"]
    mm = data["mm"]
    k = data["k"]
    x = data["x"]
    actions = data["actions"]

  print(f"CREATE with k={k}, n_k={nn}, m_k={mm}, key={x}↪")
    table = SparseTable(nn, mm, k, x)
    table.display()

  for act in actions:
        action = act["action"]
        key = act["key"]
        print(f"{action.upper()} {key}")
        if action == "insert":
            table.insert(key)
        elif action == "lookup":
            table.lookup(key)
        elif action == "delete":
            table.delete(key)

if __name__ == "__main__":
    main()
