# library-management-system
import datetime

# Sample Data
books = [
    {"id": 1, "name": "Python Basics", "author": "John Doe", "available": True},
    {"id": 2, "name": "Data Structures", "author": "Jane Smith", "available": True},
]

users = {
    "admin": {"password": "admin123", "role": "admin"},
    "user": {"password": "user123", "role": "user"}
}

issued_books = []
fines = {}


# ---------------- LOGIN ----------------
def login():
    username = input("Enter Username: ")
    password = input("Enter Password: ")

    if username in users and users[username]["password"] == password:
        print("Login Successful!\n")
        return username, users[username]["role"]
    else:
        print("Invalid Credentials\n")
        return None, None


# ---------------- SEARCH BOOK ----------------
def search_book():
    name = input("Enter book name to search: ").lower()

    results = [b for b in books if name in b["name"].lower()]

    if not results:
        print("No books found\n")
        return

    for b in results:
        status = "Available" if b["available"] else "Not Available"
        print(f'{b["id"]} | {b["name"]} | {b["author"]} | {status}')
    print()


# ---------------- ISSUE BOOK ----------------
def issue_book(username):
    book_id = int(input("Enter Book ID to issue: "))

    for b in books:
        if b["id"] == book_id and b["available"]:
            issue_date = datetime.date.today()
            return_date = issue_date + datetime.timedelta(days=7)

            issued_books.append({
                "user": username,
                "book_id": book_id,
                "issue_date": issue_date,
                "return_date": return_date
            })

            b["available"] = False
            print("Book Issued Successfully!")
            print(f"Return by: {return_date}\n")
            return

    print("Book not available\n")


# ---------------- RETURN BOOK ----------------
def return_book(username):
    book_id = int(input("Enter Book ID to return: "))

    for record in issued_books:
        if record["book_id"] == book_id and record["user"] == username:
            today = datetime.date.today()
            due = record["return_date"]

            if today > due:
                fine = (today - due).days * 5
                fines[username] = fine
                print(f"Late return! Fine = ₹{fine}")

            issued_books.remove(record)

            for b in books:
                if b["id"] == book_id:
                    b["available"] = True

            print("Book Returned Successfully\n")
            return

    print("Invalid return\n")


# ---------------- PAY FINE ----------------
def pay_fine(username):
    if username not in fines or fines[username] == 0:
        print("No pending fine\n")
        return

    print(f"Pending fine: ₹{fines[username]}")
    choice = input("Pay now? (yes/no): ")

    if choice.lower() == "yes":
        fines[username] = 0
        print("Fine Paid Successfully\n")
    else:
        print("Payment cancelled\n")


# ---------------- ADMIN: ADD BOOK ----------------
def add_book():
    name = input("Enter Book Name: ")
    author = input("Enter Author Name: ")

    new_id = len(books) + 1
    books.append({
        "id": new_id,
        "name": name,
        "author": author,
        "available": True
    })

    print("Book Added Successfully\n")


# ---------------- MENU ----------------
def user_menu(username):
    while True:
        print("1. Search Book")
        print("2. Issue Book")
        print("3. Return Book")
        print("4. Pay Fine")
        print("5. Logout")

        choice = input("Enter choice: ")

        if choice == "1":
            search_book()
        elif choice == "2":
            issue_book(username)
        elif choice == "3":
            return_book(username)
        elif choice == "4":
            pay_fine(username)
        elif choice == "5":
            break
        else:
            print("Invalid choice\n")


def admin_menu(username):
    while True:
        print("1. Search Book")
        print("2. Add Book")
        print("3. Logout")

        choice = input("Enter choice: ")

        if choice == "1":
            search_book()
        elif choice == "2":
            add_book()
        elif choice == "3":
            break
        else:
            print("Invalid choice\n")


# ---------------- MAIN ----------------
def main():
    while True:
        username, role = login()

        if role == "admin":
            admin_menu(username)
        elif role == "user":
            user_menu(username)


if __name__ == "__main__":
    main()
