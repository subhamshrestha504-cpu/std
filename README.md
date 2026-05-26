#include <iostream>
#include <iomanip>
#include "tracker.h"

void printExpenses(const std::vector<Expense>& list) {
    std::cout << std::left
              << std::setw(5)  << "ID"
              << std::setw(15) << "Category"
              << std::setw(25) << "Description"
              << std::setw(10) << "Amount"
              << std::setw(12) << "Date" << "\n";
    std::cout << std::string(67, '-') << "\n";
    for (const auto& e : list)
        std::cout << std::setw(5)  << e.id
                  << std::setw(15) << e.category
                  << std::setw(25) << e.description
                  << std::setw(10) << e.amount
                  << std::setw(12) << e.date << "\n";
}

int main() {
    Tracker tracker("data/expenses.csv");
    int choice;

    do {
        std::cout << "\n===== Expense Tracker =====\n"
                  << "1. Add Expense\n"
                  << "2. View All Expenses\n"
                  << "3. View by Month\n"
                  << "4. Monthly Summary\n"
                  << "5. Delete Expense\n"
                  << "6. Edit Expense\n"
                  << "0. Exit\n> ";
        std::cin >> choice;

        if (choice == 1) {
            std::string cat, desc, date;
            double amount;
            std::cout << "Category: ";  std::cin >> cat;
            std::cout << "Description: "; std::cin >> desc;
            std::cout << "Amount: ";    std::cin >> amount;
            std::cout << "Date (YYYY-MM-DD): "; std::cin >> date;
            tracker.addExpense(cat, desc, amount, date);
            tracker.save();
            std::cout << "✔ Expense added!\n";

        } else if (choice == 2) {
            printExpenses(tracker.getAll());

        } else if (choice == 3) {
            int m, y;
            std::cout << "Month (1-12): "; std::cin >> m;
            std::cout << "Year: ";         std::cin >> y;
            printExpenses(tracker.getByMonth(m, y));
            std::cout << "Total: $" << tracker.totalForMonth(m, y) << "\n";

        } else if (choice == 4) {
            int m, y;
            std::cout << "Month: "; std::cin >> m;
            std::cout << "Year: ";  std::cin >> y;
            auto summary = tracker.summaryByCategory(m, y);
            for (const auto& [cat, total] : summary)
                std::cout << std::setw(15) << cat << ": $" << total << "\n";
            std::cout << "Grand Total: $" << tracker.totalForMonth(m,y) << "\n";

        } else if (choice == 5) {
            int id;
            std::cout << "Enter ID to delete: "; std::cin >> id;
            tracker.deleteExpense(id) ? std::cout << "✔ Deleted\n"
                                      : std::cout << "✘ Not found\n";
            tracker.save();

        } else if (choice == 6) {
            int id; double amt; std::string desc;
            std::cout << "ID to edit: ";      std::cin >> id;
            std::cout << "New amount: ";      std::cin >> amt;
            std::cout << "New description: "; std::cin >> desc;
            tracker.editExpense(id, amt, desc) ? std::cout << "✔ Updated\n"
                                               : std::cout << "✘ Not found\n";
            tracker.save();
        }

    } while (choice != 0);

    return 0;
}