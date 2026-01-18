import os

class Customer:
    def __init__(self, cid, name, policy, nominee, phone):
        self.cid = cid
        self.name = name
        self.policy = policy
        self.nominee = nominee
        self.phone = phone

    def to_string(self):
        return f"{self.cid},{self.name},{self.policy},{self.nominee},{self.phone}\n"

    @staticmethod
    def from_string(line):
        parts = line.strip().split(",")
        if len(parts) != 5:
            return None
        return Customer(parts[0], parts[1], parts[2], parts[3], parts[4])


class InsuranceSystem:
    def __init__(self, filename="customers.txt"):
        self.filename = filename

    # -------- File Helpers --------
    def load_customers(self):
        customers = []
        if os.path.exists(self.filename):
            with open(self.filename, "r") as f:
                for line in f:
                    c = Customer.from_string(line)
                    if c:
                        customers.append(c)
        return customers

    def save_customers(self, customers):
        with open(self.filename, "w") as f:
            for c in customers:
                f.write(c.to_string())

    # -------- CRUD Operations --------
    def add_customer(self):
        cid = input("Enter Customer ID: ").strip()
        customers = self.load_customers()

        for c in customers:
            if c.cid == cid:
                print("Customer ID already exists!")
                return

        name = input("Enter Name: ").strip()
        policy = input("Enter Policy Name: ").strip()
        nominee = input("Enter Nominee Name: ").strip()
        phone = input("Enter Phone: ").strip()

        c = Customer(cid, name, policy, nominee, phone)
        with open(self.filename, "a") as f:
            f.write(c.to_string())
        print("Customer Added Successfully!")

    def view_customers(self):
        customers = self.load_customers()
        if not customers:
            print("No Records Found!")
            return
        print("\nID | Name | Policy | Nominee | Phone")
        print("-"*50)
        for c in customers:
            print(c.cid, c.name, c.policy, c.nominee, c.phone)

    def update_nominee(self):
        cid = input("Enter Customer ID to Update Nominee: ").strip()
        customers = self.load_customers()
        for c in customers:
            if c.cid == cid:
                print("Old Nominee:", c.nominee)
                c.nominee = input("Enter New Nominee: ").strip()
                self.save_customers(customers)
                print("Nominee Updated!")
                return
        print("Customer Not Found!")

    def update_customer(self):
        cid = input("Enter Customer ID to Update: ").strip()
        customers = self.load_customers()
        for c in customers:
            if c.cid == cid:
                c.name = input("Enter New Name: ").strip()
                c.policy = input("Enter New Policy: ").strip()
                c.phone = input("Enter New Phone: ").strip()
                self.save_customers(customers)
                print("Customer Updated!")
                return
        print("Customer Not Found!")

    def delete_customer(self):
        cid = input("Enter Customer ID to Delete: ").strip()
        customers = self.load_customers()
        new_list = [c for c in customers if c.cid != cid]
        if len(new_list) != len(customers):
            self.save_customers(new_list)
            print("Customer Deleted!")
        else:
            print("Customer Not Found!")

    # -------- Dashboard --------
    def dashboard(self):
        customers = self.load_customers()
        print("\n--- Insurance Dashboard ---")
        print("Total Customers:", len(customers))
        policies = {}
        for c in customers:
            policies[c.policy] = policies.get(c.policy, 0) + 1
        print("Policy Wise Count:")
        for p, count in policies.items():
            print(p, ":", count)

    # -------- Menu --------
    def menu(self):
        while True:
            print("\n--- Insurance Management System ---")
            print("1. Add Customer")
            print("2. View Customers")
            print("3. Update Customer")
            print("4. Update Nominee")
            print("5. Delete Customer")
            print("6. Dashboard")
            print("7. Exit")
            ch = input("Enter Choice: ").strip()

            if ch == "1":
                self.add_customer()
            elif ch == "2":
                self.view_customers()
            elif ch == "3":
                self.update_customer()
            elif ch == "4":
                self.update_nominee()
            elif ch == "5":
                self.delete_customer()
            elif ch == "6":
                self.dashboard()
            elif ch == "7":
                print("Thank You!")
                break
            else:
                print("Invalid Choice!")


# -------- Main --------
if __name__ == "__main__":
    app = InsuranceSystem()
    app.menu()
