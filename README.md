import java.io.*;
import java.time.LocalDate;
import java.util.*;

public class TaskManager {
    private List<Task> tasks;
    private final String fileName = "tasks.txt"; // File to store tasks

    public TaskManager() {
        tasks = new ArrayList<>();
        loadTasks(); // Load tasks from file when program starts
    }

    // Add a new task
    public void addTask(String description, LocalDate deadline) {
        Task task = new Task(description, deadline);
        tasks.add(task);
        saveTasks();
    }

    // Mark task as completed
    public void completeTask(int taskIndex) {
        if (taskIndex >= 0 && taskIndex < tasks.size()) {
            tasks.get(taskIndex).completeTask();
            saveTasks();
        } else {
            System.out.println("Invalid task number.");
        }
    }

    // Show all pending tasks
    public void showPendingTasks() {
        System.out.println("\nPending Tasks:");
        for (int i = 0; i < tasks.size(); i++) {
            if (!tasks.get(i).isCompleted()) {
                System.out.println(i + ". " + tasks.get(i));
            }
        }
    }

    // Show all completed tasks
    public void showCompletedTasks() {
        System.out.println("\nCompleted Tasks:");
        for (int i = 0; i < tasks.size(); i++) {
            if (tasks.get(i).isCompleted()) {
                System.out.println(i + ". " + tasks.get(i));
            }
        }
    }

    // Save tasks to a file for persistence
    private void saveTasks() {
        try (PrintWriter writer = new PrintWriter(new FileWriter(fileName))) {
            for (Task task : tasks) {
                writer.println(task.getDescription() + ";" + task.getDeadline() + ";" + task.isCompleted());
            }
        } catch (IOException e) {
            System.out.println("Error saving tasks to file.");
        }
    }

    // Load tasks from file when program starts
    private void loadTasks() {
        try (BufferedReader reader = new BufferedReader(new FileReader(fileName))) {
            String line;
            while ((line = reader.readLine()) != null) {
                String[] parts = line.split(";");
                String description = parts[0];
                LocalDate deadline = LocalDate.parse(parts[1]);
                boolean isCompleted = Boolean.parseBoolean(parts[2]);
                Task task = new Task(description, deadline);
                if (isCompleted) {
                    task.completeTask();
                }
                tasks.add(task);
            }
        } catch (IOException e) {
            System.out.println("No previous tasks found.");
        }
    }

    // Main menu of the Task Manager
    public void showMenu() {
        Scanner scanner = new Scanner(System.in);
        while (true) {
            System.out.println("\nTask Manager Menu:");
            System.out.println("1. Add a Task");
            System.out.println("2. View Pending Tasks");
            System.out.println("3. Mark a Task as Completed");
            System.out.println("4. View Completed Tasks");
            System.out.println("5. Exit");
            System.out.print("Enter your choice: ");
            int choice = scanner.nextInt();
            scanner.nextLine(); // Consume newline

            switch (choice) {
                case 1:
                    System.out.print("Enter task description: ");
                    String description = scanner.nextLine();
                    System.out.print("Enter deadline (YYYY-MM-DD): ");
                    LocalDate deadline = LocalDate.parse(scanner.nextLine());
                    addTask(description, deadline);
                    break;
                case 2:
                    showPendingTasks();
                    break;
                case 3:
                    System.out.print("Enter task number to mark as completed: ");
                    int taskIndex = scanner.nextInt();
                    completeTask(taskIndex);
                    break;
                case 4:
                    showCompletedTasks();
                    break;
                case 5:
                    System.out.println("Exiting Task Manager...");
                    System.exit(0);
                    break;
                default:
                    System.out.println("Invalid choice. Try again.");
            }
        }
    }

    public static void main(String[] args) {
        TaskManager manager = new TaskManager();
        manager.showMenu();
    }
}
