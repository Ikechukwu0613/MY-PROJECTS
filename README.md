# MY-PROJECTS
#collection of cool projects and my journey as a coder
import tkinter as tk
from tkinter import ttk, messagebox
from tkinter import StringVar
import os

class StudentApp:
    def __init__(self, master):
        self.master = master
        self.master.title("SS1 Extra Class")
        self.master.geometry("400x300")

        self.create_homepage()

    def create_homepage(self):
        homepage_frame = ttk.Frame(self.master, padding="10")
        homepage_frame.grid(row=0, column=0, sticky=(tk.W, tk.E, tk.N, tk.S))
        self.center_window()

        ttk.Label(homepage_frame, text="Welcome to SS1 Extra Class").grid(row=0, column=0, pady=10)

        ttk.Button(homepage_frame, text="Register a Student", command=self.show_registration).grid(row=1, column=0, pady=10, sticky=tk.W)
        ttk.Button(homepage_frame, text="Records", command=self.show_records).grid(row=2, column=0, pady=10, sticky=tk.W)
        ttk.Button(homepage_frame, text="Mark Attendance", command=self.mark_attendance).grid(row=3, column=0, pady=10, sticky=tk.W)

    def show_registration(self):
        self.clear_frame()

        registration_frame = ttk.Frame(self.master, padding="10")
        registration_frame.grid(row=0, column=0, sticky=(tk.W, tk.E, tk.N, tk.S))
        self.center_window()

        ttk.Label(registration_frame, text="Register a Student", font=("Helvetica", 16)).grid(row=0, column=0, columnspan=2, pady=10)

        ttk.Label(registration_frame, text="First Name:").grid(row=1, column=0, sticky=tk.E)
        self.first_name_entry = ttk.Entry(registration_frame)
        self.first_name_entry.grid(row=1, column=1)

        ttk.Label(registration_frame, text="Last Name:").grid(row=2, column=0, sticky=tk.E)
        self.last_name_entry = ttk.Entry(registration_frame)
        self.last_name_entry.grid(row=2, column=1)

        ttk.Label(registration_frame, text="Class:").grid(row=3, column=0, sticky=tk.E)
        self.class_var = tk.StringVar()
        class_choices = ["ARTS", "COMMERCIALS", "SCIENCES"]
        self.class_dropdown = ttk.Combobox(registration_frame, textvariable=self.class_var, values=class_choices)
        self.class_dropdown.grid(row=3, column=1)

        ttk.Label(registration_frame, text="Amount Paid:").grid(row=4, column=0, sticky=tk.E)
        self.amount_paid_entry = ttk.Entry(registration_frame)
        self.amount_paid_entry.grid(row=4, column=1)

        ttk.Button(registration_frame, text="Register", command=self.register_student).grid(row=5, columnspan=2, pady=10, sticky=tk.W)
        ttk.Button(registration_frame, text="Back", command=self.create_homepage).grid(row=6, columnspan=2, pady=10, sticky=tk.W)

    def delete_record(self, selected_class, selected_student):
        response = messagebox.askyesno("Delete Record",
                                       f"Are you sure you want to delete the record for {selected_student.replace('_', ' ')}?")
        if response:
            student_file_path = os.path.join("SS1 Extra Class", selected_class, f"{selected_student}.txt")
            os.remove(student_file_path)
            messagebox.showinfo("Record Deleted", "Record deleted successfully.")
            self.show_records()

    def edit_record(self, selected_class, selected_student):
        student_file_path = os.path.join("SS1 Extra Class", selected_class, f"{selected_student}.txt")
        with open(student_file_path, 'r') as file:
            student_details = file.readlines()

        # Extract student details from the file content
        details_dict = {}
        for line in student_details:
            key, value = line.strip().split(": ")
            details_dict[key] = value

        # Create the edit record window
        edit_record_window = tk.Toplevel(self.master)
        edit_record_window.title("Edit Record")
        edit_record_window.geometry("300x200")
        self.center_window(edit_record_window)

        ttk.Label(edit_record_window, text="Edit Record", font=("Helvetica", 16)).grid(row=0, column=0, columnspan=2,
                                                                                       pady=10)

        ttk.Label(edit_record_window, text="First Name:").grid(row=1, column=0, sticky=tk.E)
        first_name_entry = ttk.Entry(edit_record_window)
        first_name_entry.insert(0, details_dict.get("First Name", ""))
        first_name_entry.grid(row=1, column=1)

        ttk.Label(edit_record_window, text="Last Name:").grid(row=2, column=0, sticky=tk.E)
        last_name_entry = ttk.Entry(edit_record_window)
        last_name_entry.insert(0, details_dict.get("Last Name", ""))
        last_name_entry.grid(row=2, column=1)

        ttk.Label(edit_record_window, text="Class:").grid(row=3, column=0, sticky=tk.E)
        class_var = tk.StringVar()
        class_var.set(details_dict.get("Class", ""))
        class_choices = ["ARTS", "COMMERCIALS", "SCIENCES"]
        class_dropdown = ttk.Combobox(edit_record_window, textvariable=class_var, values=class_choices,
                                      state="readonly")
        class_dropdown.grid(row=3, column=1)

        ttk.Label(edit_record_window, text="Amount Paid:").grid(row=4, column=0, sticky=tk.E)
        amount_paid_entry = ttk.Entry(edit_record_window)
        amount_paid_entry.insert(0, details_dict.get("Amount Paid", ""))
        amount_paid_entry.grid(row=4, column=1)

        ttk.Button(edit_record_window, text="Save Changes",
                   command=lambda: self.save_changes(edit_record_window, selected_class, selected_student,
                                                     first_name_entry.get(), last_name_entry.get(),
                                                     class_var.get(), amount_paid_entry.get())).grid(row=5,
                                                                                                     columnspan=2,
                                                                                                     pady=10)

    # Modify the save_changes method inside the StudentApp class
    def save_changes(self, edit_record_window, selected_class, selected_student, first_name, last_name, student_class,
                     amount_paid):
        # Check if any field is empty
        if not all([first_name, last_name, student_class]):
            messagebox.showwarning("Incomplete Information", "Please fill in all fields.")
            return

        # Replace space with underscore for consistency
        selected_student_underscore = selected_student.replace(' ', '_')

        # Check if the student is already registered in the same class
        new_student_file_path = os.path.join("SS1 Extra Class", student_class, f"{first_name}_{last_name}.txt")
        if new_student_file_path != os.path.join("SS1 Extra Class", selected_class,
                                                 f"{selected_student_underscore}.txt") and os.path.exists(
                new_student_file_path):
            response = messagebox.askyesno("Student Already Registered",
                                           f"This student already exists in {student_class} class. Do you want to replace the existing record?")
            if not response:
                return

        # Save changes to the file
        with open(new_student_file_path, 'w') as file:
            file.write(f"First Name: {first_name}\n")
            file.write(f"Last Name: {last_name}\n")
            file.write(f"Class: {student_class}\n")

            # Check if amount_paid is provided and write it to the file
            if amount_paid:
                file.write(f"Amount Paid: {amount_paid}\n")

        # Remove the old file if it's different
        if new_student_file_path != os.path.join("SS1 Extra Class", selected_class,
                                                 f"{selected_student_underscore}.txt"):
            os.remove(os.path.join("SS1 Extra Class", selected_class, f"{selected_student_underscore}.txt"))

        messagebox.showinfo("Success", "Changes saved successfully.")
        edit_record_window.destroy()
        self.show_records()

    def register_student(self):
        first_name = self.first_name_entry.get()
        last_name = self.last_name_entry.get()
        student_class = self.class_var.get()
        amount_paid = self.amount_paid_entry.get()

        if not all([first_name, last_name, student_class, amount_paid]):
            messagebox.showwarning("Incomplete Information", "Please fill in all fields.")
            return

        # Ensure the SS1 Extra Class folder exists
        main_folder = "SS1 Extra Class"
        if not os.path.exists(main_folder):
            os.makedirs(main_folder)

        # Ensure the class folder exists within the SS1 Extra Class folder
        student_folder = os.path.join(main_folder, student_class)
        if not os.path.exists(student_folder):
            os.makedirs(student_folder)

        # Check if the student is already registered in the same class
        student_file_path = os.path.join(student_folder, f"{first_name}_{last_name}.txt")
        if os.path.exists(student_file_path):
            response = messagebox.askyesno(
                "Student Already Registered",
                f"This student already exists in {student_class} class. Do you want to replace the existing record?"
            )
            if not response:
                return

        # Check if the student is registered in a different class
        for class_name in ["ARTS", "COMMERCIALS", "SCIENCES"]:
            if class_name != student_class:
                other_class_file_path = os.path.join("SS1 Extra Class", class_name, f"{first_name}_{last_name}.txt")
                if os.path.exists(other_class_file_path):
                    response = messagebox.askyesno(
                        "Student Already Registered",
                        f"This student already exists in {class_name} class. Do you want to proceed with registration?"
                    )
                    if not response:
                        return

        # Save student details to a file (you can customize this part)
        with open(student_file_path, 'w') as file:
            file.write(f"First Name: {first_name}\n")
            file.write(f"Last Name: {last_name}\n")
            file.write(f"Class: {student_class}\n")
            file.write(f"Amount Paid: {amount_paid}\n")

        messagebox.showinfo("Success", "Student registered successfully.")
        self.create_homepage()

    # Inside the StudentApp class
    # Inside the StudentApp class
    # Add this method to the StudentApp class
    # Add this method to the StudentApp class
    # Modify this method inside the StudentApp class
    # Modify this method inside the StudentApp class
    # Modify this method inside the StudentApp class
    def show_records(self):
        self.clear_frame()

        records_frame = ttk.Frame(self.master, padding="10")
        records_frame.grid(row=0, column=0, sticky=(tk.W, tk.E, tk.N, tk.S))
        self.center_window()

        ttk.Label(records_frame, text="Student Records", font=("Helvetica", 16)).grid(row=0, column=0, columnspan=4,
                                                                                      pady=10)

        # Get all student files across all classes and sort them alphabetically
        all_students = []
        for class_name in ["ARTS", "COMMERCIALS", "SCIENCES"]:
            class_folder = os.path.join("SS1 Extra Class", class_name)
            student_files = [file.replace('.txt', '').replace('_', ' ') for file in os.listdir(class_folder) if
                             file.endswith(".txt")]
            all_students.extend(student_files)
        all_students.sort()

        if not all_students:
            messagebox.showinfo("No Records", "There are no student records.")
            return

        self.student_var = StringVar()
        self.student_dropdown = ttk.Combobox(records_frame, textvariable=self.student_var, values=all_students,
                                             state="readonly")
        self.student_dropdown.grid(row=1, column=0, columnspan=4, pady=10)

        ttk.Button(records_frame, text="Show Records", command=self.show_student_records).grid(row=2, column=0, pady=10,
                                                                                               columnspan=4)
        ttk.Button(records_frame, text="Edit Record",
                   command=lambda: self.edit_record(self.student_var.get())).grid(row=3, column=0, pady=10)

        ttk.Button(records_frame, text="Delete Record",
                   command=lambda: self.delete_record(self.student_var.get())).grid(row=3, column=1, pady=10)

        ttk.Button(records_frame, text="Back", command=self.create_homepage).grid(row=4, column=0, pady=10,
                                                                                  columnspan=4)

    # Add this new method inside the StudentApp class
    # Modify the edit_record method inside the StudentApp class

    # Modify this method inside the StudentApp class
    # Modify the show_student_records method as follows:

    def edit_record(self, selected_student):
        # Replace space with underscore for consistency
        selected_student_underscore = selected_student.replace(' ', '_')

        # Search for the student file in all classes
        student_file_path = None
        for class_name in ["ARTS", "COMMERCIALS", "SCIENCES"]:
            class_folder = os.path.join("SS1 Extra Class", class_name)
            student_files = [file for file in os.listdir(class_folder) if file.endswith(".txt")]
            if f"{selected_student_underscore}.txt" in student_files:
                student_file_path = os.path.join(class_folder, f"{selected_student_underscore}.txt")
                break

        if not student_file_path:
            messagebox.showwarning("Record Not Found", f"Record for {selected_student} not found.")
            return

        with open(student_file_path, 'r') as file:
            student_details = file.readlines()

        # Extract student details from the file content
        details_dict = {}
        for line in student_details:
            key, value = line.strip().split(": ")
            details_dict[key] = value

        # Create the edit record window
        edit_record_window = tk.Toplevel(self.master)
        edit_record_window.title("Edit Record")
        edit_record_window.geometry("300x200")
        self.center_window(edit_record_window)

        ttk.Label(edit_record_window, text="Edit Record", font=("Helvetica", 16)).grid(row=0, column=0, columnspan=2,
                                                                                       pady=10)

        ttk.Label(edit_record_window, text="First Name:").grid(row=1, column=0, sticky=tk.E)
        first_name_entry = ttk.Entry(edit_record_window)
        first_name_entry.insert(0, details_dict.get("First Name", ""))
        first_name_entry.grid(row=1, column=1)

        ttk.Label(edit_record_window, text="Last Name:").grid(row=2, column=0, sticky=tk.E)
        last_name_entry = ttk.Entry(edit_record_window)
        last_name_entry.insert(0, details_dict.get("Last Name", ""))
        last_name_entry.grid(row=2, column=1)

        ttk.Label(edit_record_window, text="Class:").grid(row=3, column=0, sticky=tk.E)
        class_var = tk.StringVar()
        class_var.set(details_dict.get("Class", ""))
        class_choices = ["ARTS", "COMMERCIALS", "SCIENCES"]
        class_dropdown = ttk.Combobox(edit_record_window, textvariable=class_var, values=class_choices,
                                      state="readonly")
        class_dropdown.grid(row=3, column=1)

        ttk.Label(edit_record_window, text="Amount Paid:").grid(row=4, column=0, sticky=tk.E)
        amount_paid_entry = ttk.Entry(edit_record_window)
        amount_paid_entry.insert(0, details_dict.get("Amount Paid", ""))
        amount_paid_entry.grid(row=4, column=1)

        ttk.Button(edit_record_window, text="Save Changes", command=lambda: self.save_changes(
            edit_record_window, class_var.get(), selected_student_underscore, first_name_entry.get(),
            last_name_entry.get(), class_var.get(), amount_paid_entry.get())).grid(row=5, columnspan=2, pady=10)

    def delete_record(self, selected_student):
        # Replace space with underscore for consistency
        selected_student_underscore = selected_student.replace(' ', '_')

        # Search for the student file in all classes
        student_file_path = None
        for class_name in ["ARTS", "COMMERCIALS", "SCIENCES"]:
            class_folder = os.path.join("SS1 Extra Class", class_name)
            student_files = [file for file in os.listdir(class_folder) if file.endswith(".txt")]
            if f"{selected_student_underscore}.txt" in student_files:
                student_file_path = os.path.join(class_folder, f"{selected_student_underscore}.txt")
                break

        if not student_file_path:
            messagebox.showwarning("Record Not Found", f"Record for {selected_student} not found.")
            return

        response = messagebox.askyesno("Delete Record",
                                       f"Are you sure you want to delete the record for {selected_student}?")
        if response:
            os.remove(student_file_path)
            messagebox.showinfo("Record Deleted", "Record deleted successfully.")
            self.show_records()

    # Modify this method inside the StudentApp class
    # Modify this method inside the StudentApp class
    # Modify this method inside the StudentApp class
    def show_student_records(self):
        selected_student = self.student_var.get().replace(' ', '_')  # Replace space with underscore
        if not selected_student:
            messagebox.showwarning("No Student Selected", "Please select a student.")
            return

        # Initialize selected_class before the loop
        selected_class = None

        # Determine the department of the selected student
        for class_name in ["ARTS", "COMMERCIALS", "SCIENCES"]:
            class_folder = os.path.join("SS1 Extra Class", class_name)
            student_files = [file for file in os.listdir(class_folder) if file.endswith(".txt")]
            if f"{selected_student}.txt" in student_files:
                selected_class = class_name
                break

        # Check if selected_class is assigned before proceeding
        if selected_class is None:
            messagebox.showwarning("Class Not Found",
                                   f"The class for {selected_student.replace('_', ' ')} was not found.")
            return

        student_file_path = os.path.join("SS1 Extra Class", selected_class, f"{selected_student}.txt")
        with open(student_file_path, 'r') as file:
            student_details = file.read()

        records_popup = tk.Toplevel(self.master)
        records_popup.title(f"{selected_student.replace('_', ' ')} Records")  # Replace underscore with space
        records_popup.geometry("400x300")
        self.center_window(records_popup)

        ttk.Label(records_popup, text=f"{selected_student.replace('_', ' ')} Records", font=("Helvetica", 16)).grid(
            row=0, column=0, columnspan=3, pady=10)
        ttk.Label(records_popup, text="Student Details", font=("Helvetica", 14)).grid(row=1, column=0, columnspan=3,
                                                                                      pady=5)

        details_label = ttk.Label(records_popup, text=student_details)
        details_label.grid(row=2, column=0, columnspan=3, pady=10)

        ttk.Button(records_popup, text="OK", command=records_popup.destroy).grid(row=3, column=0, columnspan=3, pady=10)

    def mark_attendance(self):
        messagebox.showinfo("Mark Attendance", "Not implemented yet.")

    def clear_frame(self):
        for widget in self.master.winfo_children():
            widget.destroy()

    def center_window(self, window=None):
        if not window:
            window = self.master
        window.update_idletasks()
        width = window.winfo_width()
        height = window.winfo_height()
        x = (window.winfo_screenwidth() // 2) - (width // 2)
        y = (window.winfo_screenheight() // 2) - (height // 2)
        window.geometry('{}x{}+{}+{}'.format(width, height, x, y))

if __name__ == "__main__":
    root = tk.Tk()
    app = StudentApp(root)
    root.mainloop()
