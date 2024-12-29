import java.io.*;
import java.util.*;

public class HospitalManagementSystem {
    private static final String PATIENTS_FILE = "patients.txt";
    private static final String DOCTORS_FILE = "doctors.txt";
    private static final String APPOINTMENTS_FILE = "appointments.txt";

    public static void main(String[] args) {
        try (Scanner scanner = new Scanner(System.in)) {
            while (true) {
                System.out.println("\nHOSPITAL MANAGEMENT SYSTEM");
                System.out.println("1. Add Patient");
                System.out.println("2. Add Doctor");
                System.out.println("3. View Patients");
                System.out.println("4. View Doctors");
                System.out.println("5. Book Appointment");
                System.out.println("6. View Appointments");
                System.out.println("7. Exit");
                System.out.print("Enter your choice: ");
                int choice = scanner.nextInt();
                scanner.nextLine(); // Consume newline

                switch (choice) {
                    case 1:
                        addPatient(scanner);
                        break;
                    case 2:
                        addDoctor(scanner);
                        break;
                    case 3:
                        viewPatients();
                        break;
                    case 4:
                        viewDoctors();
                        break;
                    case 5:
                        bookAppointment(scanner);
                        break;
                    case 6:
                        viewAppointments();
                        break;
                    case 7:
                        System.out.println("THANK YOU FOR USING HOSPITAL MANAGEMENT SYSTEM!");
                        return;
                    default:
                        System.out.println("Invalid choice! Please try again.");
                        break;
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    // Add a new patient
    public static void addPatient(Scanner scanner) {
        System.out.print("Enter Patient Name: ");
        String name = scanner.nextLine();
        System.out.print("Enter Patient Age: ");
        int age = scanner.nextInt();
        scanner.nextLine(); // Consume newline
        System.out.print("Enter Patient Gender: ");
        String gender = scanner.nextLine();

        try (FileWriter writer = new FileWriter(PATIENTS_FILE, true)) {
            writer.write(generateId() + "," + name + "," + age + "," + gender + "\n");
            System.out.println("Patient Added Successfully!");
        } catch (IOException e) {
            System.out.println("Failed to Add Patient.");
            e.printStackTrace();
        }
    }

    // Add a new doctor
    public static void addDoctor(Scanner scanner) {
        System.out.print("Enter Doctor Name: ");
        String name = scanner.nextLine();
        System.out.print("Enter Doctor Specialization: ");
        String specialization = scanner.nextLine();

        try (FileWriter writer = new FileWriter(DOCTORS_FILE, true)) {
            writer.write(generateId() + "," + name + "," + specialization + "\n");
            System.out.println("Doctor Added Successfully!");
        } catch (IOException e) {
            System.out.println("Failed to Add Doctor.");
            e.printStackTrace();
        }
    }

    // View all patients
    public static void viewPatients() {
        try (BufferedReader reader = new BufferedReader(new FileReader(PATIENTS_FILE))) {
            System.out.println("\nPatients:");
            System.out.println("+----------------------+--------------------+----------+------------+");
            System.out.println("| Patient ID          | Name               | Age      | Gender     |");
            System.out.println("+----------------------+--------------------+----------+------------+");
            String line;
            while ((line = reader.readLine()) != null) {
                String[] details = line.split(",");
                System.out.printf("| %-20s | %-18s | %-8s | %-10s |%n", details[0], details[1], details[2], details[3]);
            }
            System.out.println("+----------------------+--------------------+----------+------------+");
        } catch (FileNotFoundException e) {
            System.out.println("No patients found. Add a patient first.");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    // View all doctors
    public static void viewDoctors() {
        try (BufferedReader reader = new BufferedReader(new FileReader(DOCTORS_FILE))) {
            System.out.println("\nDoctors:");
            System.out.println("+----------------------+--------------------+------------------+");
            System.out.println("| Doctor ID           | Name               | Specialization   |");
            System.out.println("+----------------------+--------------------+------------------+");
            String line;
            while ((line = reader.readLine()) != null) {
                String[] details = line.split(",");
                System.out.printf("| %-20s | %-18s | %-16s |%n", details[0], details[1], details[2]);
            }
            System.out.println("+----------------------+--------------------+------------------+");
        } catch (FileNotFoundException e) {
            System.out.println("No doctors found. Add doctors to the file.");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    // Book an appointment
    public static void bookAppointment(Scanner scanner) {
        System.out.print("Enter Patient ID: ");
        String patientId = scanner.nextLine();
        System.out.print("Enter Doctor ID: ");
        String doctorId = scanner.nextLine();
        System.out.print("Enter Appointment Date (YYYY-MM-DD): ");
        String appointmentDate = scanner.nextLine();

        if (recordExists(PATIENTS_FILE, patientId) && recordExists(DOCTORS_FILE, doctorId)) {
            try (FileWriter writer = new FileWriter(APPOINTMENTS_FILE, true)) {
                writer.write(patientId + "," + doctorId + "," + appointmentDate + "\n");
                System.out.println("Appointment Booked Successfully!");
            } catch (IOException e) {
                System.out.println("Failed to Book Appointment.");
                e.printStackTrace();
            }
        } else {
            System.out.println("Invalid Patient ID or Doctor ID.");
        }
    }

    // View all appointments
    public static void viewAppointments() {
        try (BufferedReader reader = new BufferedReader(new FileReader(APPOINTMENTS_FILE))) {
            System.out.println("\nAppointments:");
            System.out.println("+----------------------+----------------------+-------------------+");
            System.out.println("| Patient ID          | Doctor ID           | Appointment Date |");
            System.out.println("+----------------------+----------------------+-------------------+");
            String line;
            while ((line = reader.readLine()) != null) {
                String[] details = line.split(",");
                System.out.printf("| %-20s | %-20s | %-17s |%n", details[0], details[1], details[2]);
            }
            System.out.println("+----------------------+----------------------+-------------------+");
        } catch (FileNotFoundException e) {
            System.out.println("No appointments found.");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    // Check if a record exists in a file
    public static boolean recordExists(String fileName, String id) {
        try (BufferedReader reader = new BufferedReader(new FileReader(fileName))) {
            String line;
            while ((line = reader.readLine()) != null) {
                if (line.startsWith(id + ",")) {
                    return true;
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        return false;
    }

    // Generate a random ID with 10 digits
    public static String generateId() {
        return String.format("%010d", new Random().nextInt(1_000_000_000));
    }
}
