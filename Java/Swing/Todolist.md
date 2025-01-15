![image](https://github.com/user-attachments/assets/6ba60e73-1dda-40ae-b78c-8d0576bfef9a)
![Uploading image.png…]()

![image](https://github.com/user-attachments/assets/eef1fc61-ec01-465a-ad49-b9eb6446f2d9)

### How to Build a Simple To-Do List Manager Using Java Swing

Managing tasks efficiently is essential for productivity, and what better way to learn programming than creating a simple To-Do List application? In this post, I’ll guide you through creating a Java Swing-based To-Do List application with basic functionality such as adding, viewing, and removing tasks, while ensuring the data persists between sessions.

---

### Key Features of the Application

1. **Task Management**: Users can add tasks, mark them as completed, and remove them from the list.
2. **Persistence**: Tasks are saved to a file (`tasks.txt`) and automatically loaded when the application starts.
3. **Simple and Clean UI**: Built using Java Swing components like `JList`, `JTextField`, and `JButton`.

---

### Step-by-Step Guide

#### 1. **Setting Up the UI**
The application window (`JFrame`) consists of:
- A text field (`JTextField`) for entering tasks.
- A button (`JButton`) to add tasks to the list.
- A list (`JList`) to display all tasks.
- A button to mark tasks as completed and remove them from the list.

Here's the code snippet for initializing the UI:
```java
frame = new JFrame("To-Do List Manager");
frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
frame.setSize(400, 500);

listModel = new DefaultListModel<>();
todoList = new JList<>(listModel);
JScrollPane scrollPane = new JScrollPane(todoList);

inputField = new JTextField();
JButton addButton = new JButton("Add Task");
addButton.addActionListener(e -> addTask());

JButton completeButton = new JButton("Complete Task");
completeButton.addActionListener(e -> completeTask());
```

---

#### 2. **Adding Tasks**
When users type a task into the text field and click "Add Task," the task is added to the list and saved to a file for persistence.

```java
private void addTask() {
    String task = inputField.getText().trim();
    if (!task.isEmpty()) {
        listModel.addElement(task);
        saveTasks(); // Save tasks to file
        inputField.setText("");
    }
}
```

---

#### 3. **Marking Tasks as Complete**
Users can select a task from the list and click "Complete Task" to remove it. The updated list is saved back to the file.

```java
private void completeTask() {
    int selectedIndex = todoList.getSelectedIndex();
    if (selectedIndex != -1) {
        listModel.remove(selectedIndex);
        saveTasks();
    }
}
```

---

#### 4. **Saving and Loading Tasks**
Tasks are stored in a plain text file (`tasks.txt`). When the application starts, tasks are loaded into the list. Any changes made during the session are written back to the file.

**Saving Tasks:**
```java
private void saveTasks() {
    try (BufferedWriter writer = new BufferedWriter(new FileWriter("tasks.txt"))) {
        for (int i = 0; i < listModel.getSize(); i++) {
            writer.write(listModel.getElementAt(i));
            writer.newLine();
        }
    } catch (IOException e) {
        JOptionPane.showMessageDialog(frame, "Error saving tasks: " + e.getMessage());
    }
}
```

**Loading Tasks:**
```java
private void loadTasks() {
    try (BufferedReader reader = new BufferedReader(new FileReader("tasks.txt"))) {
        String line;
        while ((line = reader.readLine()) != null) {
            listModel.addElement(line);
        }
    } catch (FileNotFoundException e) {
        // Ignore if the file doesn't exist
    } catch (IOException e) {
        JOptionPane.showMessageDialog(frame, "Error loading tasks: " + e.getMessage());
    }
}
```

---

### Running the Application
Compile and run the application:
```bash
javac TodoListGame.java
java TodoListGame
```

You will see a simple UI where you can add, view, and complete tasks. Tasks will persist even if you close and reopen the application.

---

### Future Improvements
- **Categories**: Add support for categorizing tasks (e.g., Work, Personal).
- **Due Dates**: Allow users to set due dates and sort tasks accordingly.
- **Search**: Implement a search bar to quickly find tasks.
- **Database Integration**: Store tasks in a database instead of a text file for more robust management.

---

By following this guide, you’ve created a fully functional To-Do List Manager using Java Swing. It’s a great starting point for learning GUI programming and understanding file I/O in Java. Expand it further to suit your needs and enhance your programming skills! 😊

Let me know if you have any questions or need additional features!




### FULL CODE
```java
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.io.*;

public class TodoListGame {
    private JFrame frame;
    private DefaultListModel<String> listModel;
    private JList<String> todoList;
    private JTextField inputField;

    public TodoListGame() {
        frame = new JFrame("To-Do List Manager");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(400, 500);

        // 리스트 모델과 리스트
        listModel = new DefaultListModel<>();
        loadTasks(); // Load tasks from file
        todoList = new JList<>(listModel);
        JScrollPane scrollPane = new JScrollPane(todoList);

        // 입력 필드와 추가 버튼
        inputField = new JTextField();
        JButton addButton = new JButton("Add Task");
        addButton.addActionListener(e -> addTask());

        // 완료 버튼
        JButton completeButton = new JButton("Complete Task");
        completeButton.addActionListener(e -> completeTask());

        // 레이아웃 설정
        JPanel panel = new JPanel();
        panel.setLayout(new BorderLayout());
        panel.add(inputField, BorderLayout.CENTER);
        panel.add(addButton, BorderLayout.EAST);

        JPanel bottomPanel = new JPanel();
        bottomPanel.setLayout(new GridLayout(1, 1));
        bottomPanel.add(completeButton);

        frame.setLayout(new BorderLayout());
        frame.add(scrollPane, BorderLayout.CENTER);
        frame.add(panel, BorderLayout.NORTH);
        frame.add(bottomPanel, BorderLayout.SOUTH);

        frame.setVisible(true);
    }

    private void addTask() {
        String task = inputField.getText().trim();
        if (!task.isEmpty()) {
            listModel.addElement(task);
            saveTasks(); // Save tasks to file
            inputField.setText("");
        }
    }

    private void completeTask() {
        int selectedIndex = todoList.getSelectedIndex();
        if (selectedIndex != -1) {
            listModel.remove(selectedIndex);
            saveTasks(); // Save updated tasks to file
        }
    }

    private void saveTasks() {
        try (BufferedWriter writer = new BufferedWriter(new FileWriter("tasks.txt"))) {
            for (int i = 0; i < listModel.getSize(); i++) {
                writer.write(listModel.getElementAt(i));
                writer.newLine();
            }
        } catch (IOException e) {
            JOptionPane.showMessageDialog(frame, "Error saving tasks: " + e.getMessage());
        }
    }

    private void loadTasks() {
        try (BufferedReader reader = new BufferedReader(new FileReader("tasks.txt"))) {
            String line;
            while ((line = reader.readLine()) != null) {
                listModel.addElement(line);
            }
        } catch (FileNotFoundException e) {
            // 파일이 없으면 무시 (최초 실행 시)
        } catch (IOException e) {
            JOptionPane.showMessageDialog(frame, "Error loading tasks: " + e.getMessage());
        }
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(TodoListGame::new);
    }
}

```
