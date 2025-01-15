






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
