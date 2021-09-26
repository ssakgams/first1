# first1
book
package model;

import java.util.ArrayList;
import java.util.List;

public class Board {
	// 판 크기는 정사각형 이어서 그 크기를 뿌리 크기라 하였고 이는 3이다
	private static final int ROOT = 3;
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
	public void print() {
		for (int row = 0; row < ROOT; row++) {
			for (int col = 0; col < ROOT; col++) {
				System.out.print(cells[row][col].getAttachecLineCount());
			}
			System.out.println();
		}
	}
	public static void main(String[] args) {
			Board board = new Board();
			
			board.print();
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
}

package model;

import java.util.ArrayList;
import java.util.List;

public class Line {
	
	private List<Cell> cells = new ArrayList<>();
	
	public void addCell(Cell  cell) {
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
	
}
