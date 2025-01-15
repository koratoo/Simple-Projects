
![image](https://github.com/user-attachments/assets/5b4c0ad5-436a-4b58-9887-23281b87a742)






```java
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;

public class TodoListGame {
    private JFrame frame;
    private DefaultListModel<String> listModel;
    private JList<String> todoList;
    private JTextField inputField;
    private JLabel timerLabel, scoreLabel;
    private int score = 0;
    private int timeLeft = 60; // 60초 제한
    private Timer timer;

    public TodoListGame() {
        frame = new JFrame("To-Do List Game");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(400, 500);

        // 리스트 모델과 리스트
        listModel = new DefaultListModel<>();
        todoList = new JList<>(listModel);
        JScrollPane scrollPane = new JScrollPane(todoList);

        // 입력 필드와 추가 버튼
        inputField = new JTextField();
        JButton addButton = new JButton("Add Task");
        addButton.addActionListener(e -> addTask());

        // 완료 버튼
        JButton completeButton = new JButton("Complete Task");
        completeButton.addActionListener(e -> completeTask());

        // 타이머와 점수 레이블
        timerLabel = new JLabel("Time Left: " + timeLeft + "s");
        scoreLabel = new JLabel("Score: " + score);

        // 레이아웃 설정
        JPanel panel = new JPanel();
        panel.setLayout(new BorderLayout());
        panel.add(inputField, BorderLayout.CENTER);
        panel.add(addButton, BorderLayout.EAST);

        JPanel bottomPanel = new JPanel();
        bottomPanel.setLayout(new GridLayout(1, 2));
        bottomPanel.add(completeButton);
        bottomPanel.add(timerLabel);

        frame.setLayout(new BorderLayout());
        frame.add(scrollPane, BorderLayout.CENTER);
        frame.add(panel, BorderLayout.NORTH);
        frame.add(bottomPanel, BorderLayout.SOUTH);
        frame.add(scoreLabel, BorderLayout.WEST);

        // 타이머 설정
        startTimer();

        frame.setVisible(true);
    }

    private void addTask() {
        String task = inputField.getText().trim();
        if (!task.isEmpty()) {
            listModel.addElement(task);
            inputField.setText("");
        }
    }

    private void completeTask() {
        int selectedIndex = todoList.getSelectedIndex();
        if (selectedIndex != -1) {
            listModel.remove(selectedIndex);
            score += 10; // 점수 추가
            scoreLabel.setText("Score: " + score);
        }
    }

    private void startTimer() {
        timer = new Timer(1000, new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                timeLeft--;
                timerLabel.setText("Time Left: " + timeLeft + "s");

                if (timeLeft <= 0) {
                    timer.stop();
                    JOptionPane.showMessageDialog(frame, "Time's up! Your score: " + score);
                    System.exit(0);
                }
            }
        });
        timer.start();
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(TodoListGame::new);
    }
}
```
