# =========================
# STUDENT CLASS
# =========================
class Student:
    def __init__(self, student_id, name, phone):
        self.id = student_id
        self.name = name
        self.phone = phone

# =========================
# HASH TABLE (ID-Based Search)
# =========================
class HashTable:
    def __init__(self, size=20):
        self.size = size
        self.table = [[] for _ in range(size)]  # Chaining for collisions

    def _hash(self, key):
        return key % self.size

    def insert(self, student):
        h = self._hash(student.id)
        self.table[h].append(student)

    def search(self, student_id):
        h = self._hash(student_id)
        for student in self.table[h]:
            if student.id == student_id:
                return student
        return None

    def delete(self, student_id):
        h = self._hash(student_id)
        for i, student in enumerate(self.table[h]):
            if student.id == student_id:
                self.table[h].pop(i)
                return True
        return False

# =========================
# BINARY SEARCH TREE (Name-Based Search)
# =========================
class BSTNode:
    def __init__(self, student):
        self.student = student
        self.left = None
        self.right = None

class StudentBST:
    def __init__(self):
        self.root = None

    def insert(self, student):
        if not self.root:
            self.root = BSTNode(student)
        else:
            self._insert(self.root, student)

    def _insert(self, node, student):
        if student.name < node.student.name:
            if node.left:
                self._insert(node.left, student)
            else:
                node.left = BSTNode(student)
        else:
            if node.right:
                self._insert(node.right, student)
            else:
                node.right = BSTNode(student)

    def search(self, name):
        return self._search(self.root, name)

    def _search(self, node, name):
        if not node:
            return None
        if name == node.student.name:
            return node.student
        if name < node.student.name:
            return self._search(node.left, name)
        return self._search(node.right, name)

    def inorder(self):
        students = []
        self._inorder(self.root, students)
        return students

    def _inorder(self, node, students):
        if node:
            self._inorder(node.left, students)
            students.append(node.student)
            self._inorder(node.right, students)

    # ===== BST DELETE METHOD =====
    def delete(self, name):
        self.root = self._delete(self.root, name)

    def _delete(self, node, name):
        if not node:
            return None
        if name < node.student.name:
            node.left = self._delete(node.left, name)
        elif name > node.student.name:
            node.right = self._delete(node.right, name)
        else:  # Found the node to delete
            if not node.left:
                return node.right
            if not node.right:
                return node.left
            # Node with two children: find inorder successor
            min_larger_node = self._get_min(node.right)
            node.student = min_larger_node.student
            node.right = self._delete(node.right, min_larger_node.student.name)
        return node

    def _get_min(self, node):
        while node.left:
            node = node.left
        return node

# =========================
# MERGE SORT (For Alphabetical Reports)
# =========================
def merge_sort(arr):
    if len(arr) <= 1:
        return arr
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    return merge(left, right)

def merge(left, right):
    result = []
    i = j = 0
    while i < len(left) and j < len(right):
        if left[i].name < right[j].name:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    result.extend(left[i:])
    result.extend(right[j:])
    return result

# =========================
# STUDENT MANAGER (Controller)
# =========================
class StudentManager:
    def __init__(self):
        self.hash_table = HashTable()
        self.bst = StudentBST()

    def add_student(self, student_id, name, phone):
        s = Student(student_id, name, phone)
        self.hash_table.insert(s)
        self.bst.insert(s)
        print("Student added successfully!")

    def search_by_id(self, student_id):
        student = self.hash_table.search(student_id)
        if student:
            print(f"\nFound: {student.name} | {student.phone}")
        else:
            print("\nStudent not found.")

    def search_by_name(self, name):
        student = self.bst.search(name)
        if student:
            print(f"\nFound: {student.id} | {student.phone}")
        else:
            print("\nStudent not found.")

    def delete_student(self, student_id):
        student = self.hash_table.search(student_id)
        if student:
            self.hash_table.delete(student_id)
            self.bst.delete(student.name)  # also remove from BST
            print("Student deleted.")
        else:
            print("Student not found.")

    def display_all(self):
        students = self.bst.inorder()
        print("\n--- Student List (A–Z) ---")
        for s in students:
            print(f"{s.id} | {s.name} | {s.phone}")

    def generate_sorted_report(self):
        students = self.bst.inorder()
        students = merge_sort(students)
        print("\n--- Sorted Report (Using Merge Sort) ---")
        for s in students:
            print(f"{s.id} | {s.name} | {s.phone}")

# =========================
# MAIN MENU
# =========================
def main():
    manager = StudentManager()
    while True:
        print("\n STUDENT CONTACT MANAGER ")
        print("1. Add Student")
        print("2. Search by ID")
        print("3. Search by Name")
        print("4. Delete Student")
        print("5. Display All Students")
        print("6. Generate Sorted Report")
        print("0. Exit")
        choice = input("Choose an option: ")

        if choice == "1":
            student_id = int(input("Enter ID: "))
            name = input("Enter Name: ")
            phone = input("Enter Phone: ")
            manager.add_student(student_id, name, phone)
        elif choice == "2":
            student_id = int(input("Enter ID: "))
            manager.search_by_id(student_id)
        elif choice == "3":
            name = input("Enter Name: ")
            manager.search_by_name(name)
        elif choice == "4":
            student_id = int(input("Enter ID: "))
            manager.delete_student(student_id)
        elif choice == "5":
            manager.display_all()
        elif choice == "6":
            manager.generate_sorted_report()
        elif choice == "0":
            break
        else:
            print("Invalid choice. Try again.")

if __name__ == "__main__":
    main()
