# Medical-information-system
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

// --- USER CLASS ---
class User {
    private int userId;
    private String username;
    private String role; // e.g., "doctor", "nurse", "admin"

    public User(int userId, String username, String role) {
        this.userId = userId;
        this.username = username;
        this.role = role;
    }

    @Override
    public String toString() {
        return "User ID: " + userId + ", Username: " + username + ", Role: " + role;
    }
}

// --- MEDICINE CLASS ---
class Medicine {
    private int medId;
    private String name;
    private String dosage;
    private int stock;

    public Medicine(int medId, String name, String dosage, int stock) {
        this.medId = medId;
        this.name = name;
        this.dosage = dosage;
        this.stock = stock;
    }

    public String getName() {
        return name;
    }

    @Override
    public String toString() {
        return "Med ID: " + medId + ", Name: " + name + ", Dosage: " + dosage + ", Stock: " + stock;
    }
}

// --- PATIENT CLASS ---
class Patient {
    private int patientId;
    private String name;
    private String dob;
    private String diagnosis;
    private List<Medicine> medications;

    public Patient(int patientId, String name, String dob, String diagnosis) {
        this.patientId = patientId;
        this.name = name;
        this.dob = dob;
        this.diagnosis = diagnosis;
        this.medications = new ArrayList<>();
    }

    public int getPatientId() {
        return patientId;
    }

    public String getName() {
        return name;
    }

    public void addMedication(Medicine medicine) {
        medications.add(medicine);
    }

    @Override
    public String toString() {
        StringBuilder medList = new StringBuilder();
        if (medications.isEmpty()) {
            medList.append("None");
        } else {
            for (Medicine med : medications) {
                medList.append(med.getName()).append(" ");
            }
        }
        return "Patient ID: " + patientId + ", Name: " + name + ", DOB: " + dob +
               ", Diagnosis: " + diagnosis + "\n\tMedications: [" + medList + "]";
    }
}

// --- MEDICAL SYSTEM CLASS ---
class MedicalSystem {
    private List<Patient> patients;
    private List<Medicine> medicines;
    private List<User> users;
    private int nextPatientId = 1001;
    private int nextMedId = 2001;
    private int nextUserId = 3001;

    public MedicalSystem() {
        patients = new ArrayList<>();
        medicines = new ArrayList<>();
        users = new ArrayList<>();
    }

    // --- Patient Methods ---
    public Patient addPatientRecord(String name, String dob, String diagnosis) {
        Patient patient = new Patient(nextPatientId, name, dob, diagnosis);
        patients.add(patient);
        System.out.println("Patient " + patient.getName() + " added with ID " + patient.getPatientId());
        nextPatientId++;
        return patient;
    }

    public Patient getPatientById(int patientId) {
        for (Patient p : patients) {
            if (p.getPatientId() == patientId) {
                return p;
            }
        }
        return null;
    }

    // --- Medicine Methods ---
    public Medicine addMedicineRecord(String name, String dosage, int stock) {
        Medicine medicine = new Medicine(nextMedId, name, dosage, stock);
        medicines.add(medicine);
        System.out.println("Medicine " + name + " added with ID " + nextMedId);
        nextMedId++;
        return medicine;
    }

    // --- User Methods ---
    public User addUserRecord(String username, String role) {
        User user = new User(nextUserId, username, role);
        users.add(user);
        System.out.println("User " + username + " added with ID " + nextUserId);
        nextUserId++;
        return user;
    }

    // --- Assign Medication ---
    public boolean assignMedication(int patientId, String medName) {
        Patient patient = getPatientById(patientId);
        Medicine medicine = null;
        for (Medicine m : medicines) {
            if (m.getName().equalsIgnoreCase(medName)) {
                medicine = m;
                break;
            }
        }
        if (patient != null && medicine != null) {
            patient.addMedication(medicine);
            System.out.println("Assigned " + medicine.getName() + " to patient " + patient.getName());
            return true;
        } else {
            System.out.println("Error: Patient or Medicine not found.");
            return false;
        }
    }

    public List<User> getUsers() {
        return users;
    }
}

// --- MAIN DEMO ---
public class MedicalSystemDemo {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        MedicalSystem mis = new MedicalSystem();

        System.out.println("\n================================");
        System.out.println("  MEDICAL SYSTEM CUSTOM INPUTS");
        System.out.println("================================");

        // --- 1. User Record Input ---
        System.out.println("\n--- 1. Input User Record ---");
        System.out.print("Enter a Username (e.g., Dr. Jones): ");
        String userName = sc.nextLine();
        System.out.print("Enter Role (e.g., doctor, nurse, admin): ");
        String userRole = sc.nextLine();
        mis.addUserRecord(userName, userRole);

        // --- 2. Medicine Record Input ---
        System.out.println("\n--- 2. Input Medicine Record ---");
        System.out.print("Enter Medicine Name (e.g., Ibuprofen): ");
        String medName = sc.nextLine();
        System.out.print("Enter Medicine Dosage (e.g., 400mg): ");
        String medDosage = sc.nextLine();
        System.out.print("Enter Stock Quantity (must be a number): ");
        int medStock = Integer.parseInt(sc.nextLine());
        mis.addMedicineRecord(medName, medDosage, medStock);

        // --- 3. Patient Record Input ---
        System.out.println("\n--- 3. Input Patient Record ---");
        System.out.print("Enter Patient Name: ");
        String patientName = sc.nextLine();
        System.out.print("Enter Patient DOB (YYYY-MM-DD): ");
        String patientDob = sc.nextLine();
        System.out.print("Enter Patient Diagnosis: ");
        String patientDiagnosis = sc.nextLine();
        Patient newPatient = mis.addPatientRecord(patientName, patientDob, patientDiagnosis);

        // --- 4. Assign Medication Input ---
        System.out.println("\n--- 4. Assign Medication to Patient ---");
        System.out.print("Assign '" + medName + "' to " + patientName + "? (Yes/No): ");
        String assignChoice = sc.nextLine();
        if (assignChoice.equalsIgnoreCase("yes") || assignChoice.equalsIgnoreCase("y")) {
            mis.assignMedication(newPatient.getPatientId(), medName);
        }

        System.out.println("\n================================");
        System.out.println("       FINAL RECORDS SUMMARY");
        System.out.println("================================");

        System.out.println("\n### Your Custom User ###");
        for (User u : mis.getUsers()) {
            System.out.println("* " + u);
        }

        System.out.println("\n### Your Custom Patient ###");
        System.out.println("* " + mis.getPatientById(newPatient.getPatientId()));

        sc.close();
    }
}
