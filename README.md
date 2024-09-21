# sudoku-puzzle-java-dsa
Sudoku Puzzle
import java.util.Random;
import java.util.Scanner;

public class Sudoku {
    private static final int SIZE = 9;
    private int[][] board;
    private Random random;

    public Sudoku() {
        board = new int[SIZE][SIZE];
        random = new Random();
        generateRandomBoard();
    }

    private void generateRandomBoard() {
        // Fill the diagonal 3x3 boxes first
        for (int i = 0; i < SIZE; i += 3) {
            fillDiagonalBox(i, i);
        }

        // Fill the remaining cells
        fillRemaining(0, 3);

        // Remove some elements randomly to create a puzzle
        removeElements();
    }

    private void fillDiagonalBox(int row, int col) {
        boolean[] usedNumbers = new boolean[SIZE + 1]; // 1-9

        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                int num;
                do {
                    num = random.nextInt(9) + 1; // Random number between 1-9
                } while (usedNumbers[num]);
                usedNumbers[num] = true;
                board[row + i][col + j] = num;
            }
        }
    }

    private boolean fillRemaining(int row, int col) {
        if (row == SIZE - 1 && col == SIZE) {
            return true;
        }
        if (col == SIZE) {
            row++;
            col = 0;
        }
        if (row < 3) {
            if (col < 3) {
                col = 3;
            }
        } else if (row < SIZE - 3) {
            if (col == (row / 3) * 3) {
                col += 3;
            }
        } else {
            if (col == SIZE - 3) {
                row++;
                col = 0;
                if (row == SIZE) {
                    return true;
                }
            }
        }

        for (int num = 1; num <= SIZE; num++) {
            if (isValidMove(row, col, num)) {
                board[row][col] = num;
                if (fillRemaining(row, col + 1)) {
                    return true;
                }
                board[row][col] = 0;
            }
        }
        return false;
    }

    private void removeElements() {
        int cellsToRemove = 20 + random.nextInt(11); // Randomly remove between 20-30 elements
        while (cellsToRemove > 0) {
            int row = random.nextInt(SIZE);
            int col = random.nextInt(SIZE);
            if (board[row][col] != 0) {
                board[row][col] = 0;
                cellsToRemove--;
            }
        }
    }

    public void displayBoard() {
        for (int i = 0; i < SIZE; i++) {
            for (int j = 0; j < SIZE; j++) {
                System.out.print(board[i][j] + " ");
            }
            System.out.println();
        }
    }

    public boolean isValidMove(int row, int col, int num) {
        // Check row and column
        for (int i = 0; i < SIZE; i++) {
            if (board[row][i] == num || board[i][col] == num) {
                return false;
            }
        }
        // Check 3x3 box
        int boxRow = row - row % 3;
        int boxCol = col - col % 3;
        for (int i = boxRow; i < boxRow + 3; i++) {
            for (int j = boxCol; j < boxCol + 3; j++) {
                if (board[i][j] == num) {
                    return false;
                }
            }
        }
        return true;
    }

    public void makeMove(int row, int col, int num) {
        board[row][col] = num;
    }

    public boolean isBoardComplete() {
        for (int i = 0; i < SIZE; i++) {
            for (int j = 0; j < SIZE; j++) {
                if (board[i][j] == 0) {
                    return false;
                }
            }
        }
        return true;
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        Sudoku sudoku = new Sudoku();

        while (true) {
            sudoku.displayBoard();
            System.out.print("Enter row (0-8), column (0-8), and number (1-9) or -1 to quit: ");

            int row = scanner.nextInt();
            if (row == -1) break;

            int col = scanner.nextInt();
            int num = scanner.nextInt();

            if (row < 0 || row >= SIZE || col < 0 || col >= SIZE || num < 1 || num > 9) {
                System.out.println("Invalid input. Please enter row and column in range (0-8) and number in range (1-9).");
                continue;
            }

            if (sudoku.isValidMove(row, col, num)) {
                sudoku.makeMove(row, col, num);
                if (sudoku.isBoardComplete()) {
                    sudoku.displayBoard();
                    System.out.println("Congratulations! You've completed the Sudoku puzzle!");
                    break;
                }
            } else {
                System.out.println("Invalid move. Try again.");
            }
        }
        scanner.close();
    }
}
