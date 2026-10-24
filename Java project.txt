
import java.util.Scanner;

public class LoanEligibilityEMITrackingSystem {

    static Scanner sc = new Scanner(System.in);

    static String name;
    static int age, tenure, totalEMI, paidEMI = 0;
    static double income, loan, rate, existingEMI;
    static double emi, interest, totalPayment;
    static boolean applied = false;
    static boolean eligible = false;

    public static void main(String[] args) {
        int choice;

        do {
            System.out.println("\n====================================");
            System.out.println(" LOAN ELIGIBILITY & EMI TRACKING");
            System.out.println("====================================");
            System.out.println("1. Apply for Loan");
            System.out.println("2. Check Eligibility");
            System.out.println("3. Calculate EMI");
            System.out.println("4. Record EMI Payment");
            System.out.println("5. View Loan Details");
            System.out.println("6. View EMI Tracking");
            System.out.println("7. Exit");
            System.out.print("Enter your choice: ");

            choice = readInt();

            switch (choice) {
                case 1:
                    applyLoan();
                    break;
                case 2:
                    checkEligibility();
                    break;
                case 3:
                    calculateEMI();
                    break;
                case 4:
                    recordPayment();
                    break;
                case 5:
                    viewDetails();
                    break;
                case 6:
                    viewTracking();
                    break;
                case 7:
                    System.out.println("Thank you!");
                    break;
                default:
                    System.out.println("Invalid choice!");
            }
        } while (choice != 7);

        sc.close();
    }

    static int readInt() {
        while (!sc.hasNextInt()) {
            System.out.print("Enter a valid integer: ");
            sc.next();
        }
        return sc.nextInt();
    }

    static double readDouble() {
        while (!sc.hasNextDouble()) {
            System.out.print("Enter a valid number: ");
            sc.next();
        }
        return sc.nextDouble();
    }

    static void applyLoan() {
        sc.nextLine();

        System.out.println("\n--- LOAN APPLICATION ---");
        System.out.print("Enter customer name: ");
        name = sc.nextLine().trim();

        if (name.isEmpty()) {
            System.out.println("Name cannot be empty.");
            return;
        }

        System.out.print("Enter age: ");
        age = readInt();

        System.out.print("Enter monthly income: ");
        income = readDouble();

        System.out.print("Enter loan amount: ");
        loan = readDouble();

        System.out.print("Enter annual interest rate (%): ");
        rate = readDouble();

        System.out.print("Enter tenure in years: ");
        tenure = readInt();

        System.out.print("Enter existing monthly EMI: ");
        existingEMI = readDouble();

        if (age < 18 || age > 100 || income <= 0 ||
            loan <= 0 || rate < 0 || rate > 100 ||
            tenure <= 0 || tenure > 50 || existingEMI < 0) {
            System.out.println("Invalid loan details!");
            applied = false;
            eligible = false;
            paidEMI = 0;
            return;
        }

        applied = true;
        eligible = false;
        paidEMI = 0;

        System.out.println("Application submitted successfully!");
    }

    static void calculateValues() {
        totalEMI = tenure * 12;
        double monthlyRate = rate / 1200;

        if (monthlyRate == 0) {
            emi = loan / totalEMI;
        } else {
            double factor = Math.pow(1 + monthlyRate, totalEMI);
            emi = loan * monthlyRate * factor / (factor - 1);
        }

        totalPayment = emi * totalEMI;
        interest = totalPayment - loan;
    }

    static void checkEligibility() {
        if (!applied) {
            System.out.println("Apply for a loan first!");
            return;
        }

        calculateValues();

        double allowedEMI = income * 0.40;
        double totalMonthlyEMI = emi + existingEMI;

        System.out.println("\n--- ELIGIBILITY CHECK ---");
        System.out.printf("Monthly Income: Rs. %.2f%n", income);
        System.out.printf("Proposed EMI: Rs. %.2f%n", emi);
        System.out.printf("Existing EMI: Rs. %.2f%n", existingEMI);
        System.out.printf("Allowed EMI Limit: Rs. %.2f%n", allowedEMI);

        if (age >= 21 && totalMonthlyEMI <= allowedEMI) {
            eligible = true;
            System.out.println("Status: ELIGIBLE");
        } else {
            eligible = false;
            System.out.println("Status: NOT ELIGIBLE");
        }
    }

    static void calculateEMI() {
        if (!applied) {
            System.out.println("Apply for a loan first!");
            return;
        }

        calculateValues();

        System.out.println("\n--- EMI CALCULATION ---");
        System.out.printf("Loan Amount: Rs. %.2f%n", loan);
        System.out.printf("Annual Interest: %.2f%%%n", rate);
        System.out.println("Tenure: " + tenure + " years");
        System.out.println("Total EMIs: " + totalEMI);
        System.out.printf("Monthly EMI: Rs. %.2f%n", emi);
        System.out.printf("Total Interest: Rs. %.2f%n", interest);
        System.out.printf("Total Payment: Rs. %.2f%n", totalPayment);
    }

    static void recordPayment() {
        if (!applied || !eligible) {
            System.out.println("Apply and meet eligibility criteria first!");
            return;
        }

        calculateValues();

        if (paidEMI >= totalEMI) {
            System.out.println("All EMIs have been completed!");
            return;
        }

        System.out.println("\n--- RECORD EMI PAYMENT ---");
        System.out.println("Next EMI Number: " + (paidEMI + 1));
        System.out.printf("EMI Amount: Rs. %.2f%n", emi);
        System.out.print("Confirm payment (1 = Yes, 0 = No): ");

        int confirm = readInt();

        if (confirm == 1) {
            paidEMI++;
            System.out.println("Payment recorded successfully!");
        } else {
            System.out.println("Payment not recorded.");
        }
    }

    static void viewDetails() {
        if (!applied) {
            System.out.println("No loan application found!");
            return;
        }

        calculateValues();

        System.out.println("\n--- LOAN DETAILS ---");
        System.out.println("Customer Name: " + name);
        System.out.println("Age: " + age);
        System.out.printf("Monthly Income: Rs. %.2f%n", income);
        System.out.printf("Loan Amount: Rs. %.2f%n", loan);
        System.out.printf("Interest Rate: %.2f%%%n", rate);
        System.out.println("Tenure: " + tenure + " years");
        System.out.println("Eligibility: " +
                (eligible ? "ELIGIBLE" : "NOT ELIGIBLE"));
        System.out.printf("Monthly EMI: Rs. %.2f%n", emi);
        System.out.printf("Total Interest: Rs. %.2f%n", interest);
        System.out.printf("Total Payment: Rs. %.2f%n", totalPayment);
    }

    static void viewTracking() {
        if (!applied) {
            System.out.println("Apply for a loan first!");
            return;
        }

        calculateValues();

        int remaining = totalEMI - paidEMI;
        double amountPaid = paidEMI * emi;
        double amountRemaining = remaining * emi;

        System.out.println("\n--- EMI TRACKING ---");
        System.out.println("Customer: " + name);
        System.out.println("Total EMIs: " + totalEMI);
        System.out.println("Paid EMIs: " + paidEMI);
        System.out.println("Remaining EMIs: " + remaining);
        System.out.printf("Monthly EMI: Rs. %.2f%n", emi);
        System.out.printf("Amount Paid: Rs. %.2f%n", amountPaid);
        System.out.printf("Remaining Payments: Rs. %.2f%n",
                amountRemaining);

        if (remaining == 0) {
            System.out.println("Loan Status: COMPLETED");
        } else if (paidEMI > 0) {
            System.out.println("Loan Status: IN PROGRESS");
        } else {
            System.out.println("Loan Status: NOT STARTED");
        }
    }
}
