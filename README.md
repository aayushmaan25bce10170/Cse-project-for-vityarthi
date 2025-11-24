# Cse-project-for-vityarthi
import tkinter as tk
from tkinter import messagebox, font
import math               # NEW: For rounding or other math functions
import datetime           # NEW: For timestamp, display current date
import json               # NEW: For saving/loading budget data

def calculate_budget():
    try:
        income = float(entry_income.get())
        rent_pct = float(entry_rent.get())
        groceries_pct = float(entry_groceries.get())
        utilities_pct = float(entry_utilities.get())
        transport_pct = float(entry_transport.get())
        misc_pct = float(entry_misc.get())

        total_pct = rent_pct + groceries_pct + utilities_pct + transport_pct + misc_pct

        rent_amt = math.ceil(income * rent_pct / 100)        # Using math.ceil for rounding up
        groceries_amt = math.ceil(income * groceries_pct / 100)
        utilities_amt = math.ceil(income * utilities_pct / 100)
        transport_amt = math.ceil(income * transport_pct / 100)
        misc_amt = math.ceil(income * misc_pct / 100)

        total_expenses = rent_amt + groceries_amt + utilities_amt + transport_amt + misc_amt
        balance = income - total_expenses

        label_rent_amt.config(text=f"Rent: ₹{rent_amt}")
        label_groceries_amt.config(text=f"Groceries: ₹{groceries_amt}")
        label_utilities_amt.config(text=f"Utilities: ₹{utilities_amt}")
        label_transport_amt.config(text=f"Transport: ₹{transport_amt}")
        label_misc_amt.config(text=f"Miscellaneous: ₹{misc_amt}")

        label_total_expenses.config(text=f"Total Expenses: ₹{total_expenses}")
        label_balance.config(text=f"Remaining Balance: ₹{balance}")
        label_date.config(text=f"Date: {datetime.datetime.now().strftime('%d-%b-%Y %H:%M:%S')}")  # Show current date/time

        if total_pct > 100:
            warning_label.config(text="Warning: Total percentage exceeds 100!", fg="#d7263d")
            label_total_expenses.config(fg="#d7263d")
            label_balance.config(fg="#d7263d")
        else:
            warning_label.config(text="")
            label_total_expenses.config(fg="#23395d")
            label_balance.config(fg="#117a65" if balance >= 0 else "#d7263d")

        # Save data to a JSON file
        budget_data = {
            "income": income,
            "rent_pct": rent_pct,
            "groceries_pct": groceries_pct,
            "utilities_pct": utilities_pct,
            "transport_pct": transport_pct,
            "misc_pct": misc_pct,
            "date": datetime.datetime.now().isoformat(),
            "total_expenses": total_expenses,
            "balance": balance
        }
        with open("budget_data.json", "w") as f:
            json.dump(budget_data, f, indent=4)

    except ValueError:
        messagebox.showerror("Input Error", "Please enter valid numbers for all fields.")

def clear_inputs():
    entry_income.delete(0, tk.END)
    entry_rent.delete(0, tk.END)
    entry_groceries.delete(0, tk.END)
    entry_utilities.delete(0, tk.END)
    entry_transport.delete(0, tk.END)
    entry_misc.delete(0, tk.END)
    label_rent_amt.config(text="Rent: ₹0.00")
    label_groceries_amt.config(text="Groceries: ₹0.00")
    label_utilities_amt.config(text="Utilities: ₹0.00")
    label_transport_amt.config(text="Transport: ₹0.00")
    label_misc_amt.config(text="Miscellaneous: ₹0.00")
    label_total_expenses.config(text="Total Expenses: ₹0.00", fg="#23395d")
    label_balance.config(text="Remaining Balance: ₹0.00", fg="#23395d")
    warning_label.config(text="")
    label_date.config(text="Date: --")

# Color scheme (unchanged)
BG_MAIN = "#e9f1fa"
BG_FRAME = "#ced3f6"
FG_HEADER = "#353866"
FG_LABEL = "#474787"
FG_BUTTON = "#ffffff"
BG_BUTTON = "#6a89cc"
FG_RESULT = "#23395d"
FG_WARNING = "#d7263d"

root = tk.Tk()
root.title("Monthly Budget Planner - Percent Based")
root.geometry("520x700")
root.config(bg=BG_MAIN)

header_font = font.Font(family="Helvetica", size=20, weight="bold")
label_font = font.Font(family="Helvetica", size=12)
result_font = font.Font(family="Helvetica", size=14, weight="bold")

header = tk.Label(root, text="Monthly Budget Planner", bg=BG_MAIN, fg=FG_HEADER, font=header_font)
header.pack(pady=20)

frame_inputs = tk.Frame(root, bg=BG_FRAME, bd=2, relief="ridge")
frame_inputs.pack(padx=20, pady=10, fill="x")

tk.Label(frame_inputs, text="Monthly Income (₹):", bg=BG_FRAME, fg=FG_LABEL, font=label_font, anchor="w").pack(fill="x", padx=10, pady=(10, 0))
entry_income = tk.Entry(frame_inputs, font=label_font)
entry_income.pack(fill="x", padx=10, pady=(0, 10))

tk.Label(frame_inputs, text="Enter percentage of income for each category:", bg=BG_FRAME, fg=FG_HEADER, font=label_font).pack(padx=10, pady=(5, 0))

entry_rent = tk.Entry(frame_inputs, font=label_font)
entry_groceries = tk.Entry(frame_inputs, font=label_font)
entry_utilities = tk.Entry(frame_inputs, font=label_font)
entry_transport = tk.Entry(frame_inputs, font=label_font)
entry_misc = tk.Entry(frame_inputs, font=label_font)

fields_entries = [
    ("Rent (%)", entry_rent),
    ("Groceries (%)", entry_groceries),
    ("Utilities (%)", entry_utilities),
    ("Transport (%)", entry_transport),
    ("Miscellaneous (%)", entry_misc),
]

for label_text, entry_widget in fields_entries:
    tk.Label(frame_inputs, text=label_text, bg=BG_FRAME, fg=FG_LABEL, font=label_font, anchor="w").pack(fill="x", padx=10, pady=(10, 0))
    entry_widget.pack(fill="x", padx=10, pady=(0, 10))

btn_calculate = tk.Button(root, text="Calculate Budget", bg=BG_BUTTON, fg=FG_BUTTON, font=label_font, command=calculate_budget)
btn_calculate.pack(pady=(18, 9), ipadx=10, ipady=5)

btn_clear = tk.Button(root, text="Clear", bg=FG_HEADER, fg="white", font=label_font, command=clear_inputs)
btn_clear.pack(pady=(0, 18), ipadx=10, ipady=5)

# Results Area (visible on startup)
label_rent_amt = tk.Label(root, text="Rent: ₹0.00", font=result_font, bg=BG_MAIN, fg=FG_RESULT)
label_rent_amt.pack()
label_groceries_amt = tk.Label(root, text="Groceries: ₹0.00", font=result_font, bg=BG_MAIN, fg=FG_RESULT)
label_groceries_amt.pack()
label_utilities_amt = tk.Label(root, text="Utilities: ₹0.00", font=result_font, bg=BG_MAIN, fg=FG_RESULT)
label_utilities_amt.pack()
label_transport_amt = tk.Label(root, text="Transport: ₹0.00", font=result_font, bg=BG_MAIN, fg=FG_RESULT)
label_transport_amt.pack()
label_misc_amt = tk.Label(root, text="Miscellaneous: ₹0.00", font=result_font, bg=BG_MAIN, fg=FG_RESULT)
label_misc_amt.pack()
label_total_expenses = tk.Label(root, text="Total Expenses: ₹0.00", font=result_font, bg=BG_MAIN, fg=FG_RESULT)
label_total_expenses.pack(pady=(10, 0))
label_balance = tk.Label(root, text="Remaining Balance: ₹0.00", font=result_font, bg=BG_MAIN, fg=FG_RESULT)
label_balance.pack(pady=(0, 10))
label_date = tk.Label(root, text="Date: --", font=label_font, fg=FG_LABEL, bg=BG_MAIN)
label_date.pack()
warning_label = tk.Label(root, text="", font=label_font, fg=FG_WARNING, bg=BG_MAIN)
warning_label.pack(pady=8)

root.mainloop()
