package control;

import java.util.Scanner;

import model.Board;
import model.Stone;
import view.BoardViewer;

public class BoardController {
	private static Scanner in = new Scanner(System.in);

	public static void main(String[] args) {
		boolean userTurn = decideTurn(); // 함수이름 decideTurn, 리턴타입 boolean

		// 일단 해당 차례의 플레이어가 착점을 하고
		// 승부 판단이 나거나 또는 다채워지면 비겼다고 출력하고
		// 프로그램을 끝낸다.
		WinLoseStatus winLoseStatus;
		do {
			BoardViewer.display();
			// 턴 소유자가 착점을 합니다.
			if (userTurn) {
				// 사용자의 입력을 바탕으로 빈 곳을 지정하였는 지 검사하고 그곳에 Black을 착점
				doUserPlay();
			} else {
				// 깊은 내부 정보 탐색을 개발하면서 점진적으로 똑똑하게 만들면서 가지고 놀자
			}
			// 턴의 소유권을 상대에게 넘깁니다.
			userTurn = !userTurn;
		} while ((winLoseStatus = decideWin()) == null);

	}

	private static void doUserPlay() {
		Board board  = Board.getInstance();
		int row;  in.nextInt();
		int col;  in.nextInt();		
		boolean canDeposite;
		do {
			System.out.println("플레이어님 빈 곳을 찾아 검은돌을 놓으세요.");
			row = in.nextInt();
			col = in.nextInt();
			canDeposite = board.canDeposite(row, col);
			if(!canDeposite) {
				System.out.println("잘 쫌 쫌!");
			}
		} while (!canDeposite);
			board.deposite(row, col, Stone.Black);
	}

	private static WinLoseStatus decideWin() {
		// 어떤 줄이 Black으로 가득 채워진 상태면 User
		// 어떤 줄이 White로 가득 채워진 상태면 Computer
		// 9개의 칸이 가득 채워진 상태면 Draw
		return null;
	}

	/**
	 * 누가 먼저 놓을 지 확률 50%로 결정합니다.
	 * 
	 * @return
	 */
	private static boolean decideTurn() {
		return Math.random() >= 0.5; // 연산자를 올바르게 쓰도록 노력
	}

}

package control;

public enum WinLoseStatus {
	User, Computer, Draw;
}

package model;

import java.util.ArrayList;
import java.util.List;

public class Board {
	// 판 크기는 정사각형 이어서 그 크기를 뿌리 크기라 하였고 이는 3이다
	public static final int ROOT = 3;
	private static Board theInstance = new Board();

	// 전체 9칸이 2차원 배열 구조로 만들어 놓았다.
	private Cell[][] cells = new Cell[ROOT][ROOT];
	// 전체 9칸을 만드는 과정에서 수평, 수직 3개씩 그리고 대각줄 2개 만들것이야
	private List<Line> lines = new ArrayList<>();

	/**
	 * 생성자 : 만들어지는 과정을 문장으로 채우는 곳
	 */
	public Board() {
		// 9개의 칸을 만들어서 이를 cells에 담을 것
		for (int row = 0; row < ROOT; row++) {
			for (int col = 0; col < ROOT; col++) {
				cells[row][col] = new Cell();

			}
		}
		// 수평 줄 만들기
		for (int row = 0; row < ROOT; row++) {
			Line aHoriLine = new Line();
			for (int col = 0; col < ROOT; col++) {
				// 객체간 협력
				aHoriLine.addCell(cells[row][col]);

			}
			lines.add(aHoriLine);
		}
		// 수직 줄 만들기
		for (int col = 0; col < ROOT; col++) {
			Line aVertLine = new Line();
			for (int row = 0; row < ROOT; row++) {
				aVertLine.addCell(cells[row][col]);
			}
			lines.add(aVertLine);
		}
		// -1 기울기의 대각줄 만들기
		Line aDiogLine = new Line();
		for (int idx = 0; idx < ROOT; idx++) {
			aDiogLine.addCell(cells[idx][idx]);
		}
		lines.add(aDiogLine);

		// 1기울기의 대각줄 만들기
		aDiogLine = new Line();
		for (int idx = 0; idx < ROOT; idx++) {
			aDiogLine.addCell(cells[idx][ROOT - idx - 1]);
		}
		lines.add(aDiogLine);
	}

	public static Board getInstance() {
		return theInstance;
	}

	public Cell[][] getCells() {
		return cells;
	}

	public void deposite(int row, int col, Stone black) {
		cells[row][col].deposite(black);
	}
	/**
	 * row, col은 일단 그 값이 >= 0 && < ROOT 의 범위 안에 있어야 합니다.
	 * 잘못 개발되면 예외발생.. 14장. ArrayIndexOutOfBounds
	 * 그리고 그 칸의 돌은 null이어야 합니다.
	 * @param row
	 * @param col
	 * @return
	 */
	public boolean canDeposite(int row, int col) {
		return isInBound(row) ?
				isInBound(col) ?
						!cells[row][col].hasStone()
						: false
					: false;  
	}
	
	private boolean isInBound(int idx) {
		return idx >= 0 && idx <ROOT;
	}
}

package model;

import java.util.ArrayList;
import java.util.List;

public class Cell {
	private Stone 놓인돌 = null;
	private List<Line> lines = new ArrayList<>();

	public void addline(Line line) {
		lines.add(line);

	}
	
	public int getAttachecLineCount() {
		return lines.size();
	}
	
	public Stone getStone() {
		return 놓인돌;
	}
	public void deposite(Stone black) {
		놓인돌 = black;
	}

	public boolean hasStone() {
		return 놓인돌 != null;
	}
}

package model;

import java.util.ArrayList;
import java.util.List;

public class Line {
	private List<Cell> cells = new ArrayList<>();

	public void addCell(Cell cell) {
		cells.add(cell);
		cell.addline(this);
	}
}

package model;

public enum Stone {
	Black('O'), White('X');
	
	private char shape;

	private Stone(char shape) {
		this.shape = shape;
	}	
	
	public char getShape() {
		return shape;
	}
}
	
package view;

import model.Board;
import model.Cell;

public class BoardViewer {

	public static void display() {
		Board board  = Board.getInstance();

		//------------- 제일 위의 뚜껑으로 덮자
		String coverLine = genCoverLine(); // 변수수립해서 기억.
		System.out.print(coverLine);	   // 뚜껑 출력
		
		String emptyLine = genEmptyLine();
		String interLine = genInterLine();
		
		boolean isFirstLine = true;		
		for (Cell[] row : board.getCells()) {
			if (isFirstLine) {
				isFirstLine = false;
			} else {
				System.out.print(interLine);
			}
			
			System.out.print(emptyLine);
			System.out.print('|');
			for (Cell cell : row) {
				System.out.print(' ');
				if (cell.getStone() == null) {
					System.out.print(' ');
				} else {
					System.out.print(cell.getStone().getShape());
				}
				System.out.print(' ');
				System.out.print('|');
			}
			
			System.out.println();
			System.out.print(emptyLine);
		}
		//------------- 제일 아래 뚜껑으로 닫자
		System.out.println(coverLine);
	}

	// private : 이 클래스 안에서만 사용
	// static : static한 함수에서는 static한 함수 만 부를 수 있다.
	private static String genCoverLine() {
		StringBuilder sb = new StringBuilder();

		for (int i = 0; i < Board.ROOT * 4 + 1; i++) {
			sb.append('-');
		}
		sb.append('\n');	//new line. 줄바꿈 문자
		return sb.toString();
	}
	/**
	 * | + 3 * [space + space + space + |] 
	 * @return
	 */
	private static String genEmptyLine() {
		StringBuilder sb = new StringBuilder("|");

		for (int i = 0; i < Board.ROOT; i++) {
			sb.append("   |");
		}
		sb.append('\n');	//new line. 줄바꿈 문자
		return sb.toString();
	}
	/**
	 * | (2[---+])---| 
	 * @return
	 */
	private static String genInterLine() {
		StringBuilder sb = new StringBuilder("|");

		for (int i = 0; i < Board.ROOT; i++) {
			sb.append("---");
			if (i < Board.ROOT - 1) {
				sb.append('+'); 
			} else {
				sb.append('|');
			}
		}
		sb.append('\n');	//new line. 줄바꿈 문자
		return sb.toString();
	}

}
