import sqlite3

# -------------------- DATABASE CONNECTION --------------------
conn = sqlite3.connect("company.db")
cursor = conn.cursor()

# -------------------- CREATE TABLE --------------------
cursor.execute("""
CREATE TABLE IF NOT EXISTS employees (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    role TEXT NOT NULL,
    salary REAL NOT NULL,
    email TEXT NOT NULL
)
""")
conn.commit()

# -------------------- FUNCTIONS --------------------
def add_employee():
    name = input("Enter Employee Name: ")
    role = input("Enter Employee Role: ")
    salary = float(input("Enter Employee Salary: "))
    email = input("Enter Employee Email: ")

    cursor.execute(
        "INSERT INTO employees (name, role, salary, email) VALUES (?, ?, ?, ?)",
        (name, role, salary, email)
    )
    conn.commit()
    print("✅ Employee added successfully!")

def view_employees():
    cursor.execute("SELECT * FROM employees")
    rows = cursor.fetchall()

    if not rows:
        print("⚠ No employees found.")
        return

    print("\nID | Name | Role | Salary | Email")
    print("-" * 50)
    for row in rows:
        print(f"{row[0]} | {row[1]} | {row[2]} | {row[3]} | {row[4]}")

def search_employee():
    emp_id = int(input("Enter Employee ID: "))
    cursor.execute("SELECT * FROM employees WHERE id = ?", (emp_id,))
    row = cursor.fetchone()

    if row:
        print("\nEmployee Found")
        print("-" * 30)
        print(f"ID     : {row[0]}")
        print(f"Name   : {row[1]}")
        print(f"Role   : {row[2]}")
        print(f"Salary : {row[3]}")
        print(f"Email  : {row[4]}")
    else:
        print("❌ Employee not found.")

def update_employee():
    emp_id = int(input("Enter Employee ID to Update: "))
    salary = float(input("Enter New Salary: "))

    cursor.execute(
        "UPDATE employees SET salary = ? WHERE id = ?",
        (salary, emp_id)
    )
    conn.commit()

    if cursor.rowcount:
        print("✅ Employee updated successfully!")
    else:
        print("❌ Employee not found.")

def delete_employee():
    emp_id = int(input("Enter Employee ID to Delete: "))

    cursor.execute("DELETE FROM employees WHERE id = ?", (emp_id,))
    conn.commit()

    if cursor.rowcount:
        print("✅ Employee deleted successfully!")
    else:
        print("❌ Employee not found.")

# -------------------- MAIN MENU --------------------
while True:
    print("\n========== EMPLOYEE MANAGEMENT SYSTEM ==========")
    print("1. Add Employee")
    print("2. View All Employees")
    print("3. Search Employee by ID")
    print("4. Update Employee Salary")
    print("5. Delete Employee")
    print("6. Exit")
    print("==============================================")

    choice = input("Enter your choice: ")

    if choice == "1":
        add_employee()
    elif choice == "2":
        view_employees()
    elif choice == "3":
        search_employee()
    elif choice == "4":
        update_employee()
    elif choice == "5":
        delete_employee()
    elif choice == "6":
        print("👋 Thank you for using Employee Management System")
        break
    else:
        print("⚠ Invalid choice. Try again.")

conn.close()
