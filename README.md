# Week-10h
Tugas Artificial Intelligent and Aplication Week 10

Nama	          	: Almas Diqya Wafa’
NIM		           : 5311421005
Prodi		         : Teknik Elektro
Mata Kuliah	    : Artificial Intellegent and Aplication
Rombel	         : Senin 09.00

MODUL 5
“Teknik Heuristic Search”


//EightPuzzleSearch4//
import java.util.Vector;

public class EightPuzzleSearch4 {
    EightPuzzleSpace4 space = new EightPuzzleSpace4();

    Vector<Node> open = new Vector<Node>();
    Vector<Node> closed = new Vector<Node>();

    int h1Cost(Node node) {
        int cost = 0;
        for (int i = 0; i < node.state.length; i++) {
            if (node.state[i] != i) cost++;
        }
        return cost;
    }

    int h2Cost(Node node) {
        int cost = 0;
        int state[] = node.state;
        for (int i = 0; i < state.length; i++) {
            int v0 = i, v1 = state[i];
            // Tidak menghitung ubin yang kosong
            if (v1 == 0) continue;
            int row0 = v0 / 3, col0 = v0 % 3, row1 = v1 / 3, col1 = v1 % 3;
            int c = (Math.abs(row0 - row1) + Math.abs(col0 - col1));
            cost += c;
        }
        return cost;
    }

    // Boleh diubah dengan memakai heuristic h1 atau h2
    int hCost(Node node) {
        return h2Cost(node);
    }

    Node getBestNode(Vector nodes) {
        int index = 0;
        int minCost = Integer.MAX_VALUE;
        for (int i = 0; i < nodes.size(); i++) {
            Node node = (Node) nodes.elementAt(i);
            if (node.cost < minCost) {
                minCost = node.cost;
                index = i;
            }
        }
        Node bestNode = (Node) nodes.remove(index);
        return bestNode;
    }

    int getPreviousCost(Node node) {
        int i = open.indexOf(node);
        int cost = Integer.MAX_VALUE;
        if (i != -1) {
            cost = open.get(i).cost;
        } else {
            i = closed.indexOf(node);
            if (i != -1) cost = closed.get(i).cost;
        }
        return cost;
    }

    void printPath(Vector path) {
        for (int i = 0; i < path.size(); i++) {
            System.out.print(" " + path.elementAt(i) + "\n");
        }
    }

    void run() {
        Node root = space.getRoot();
        Node goal = space.getGoal();
        Node solution = null;
        open.add(root);
        System.out.print("\nRoot: " + root + "\n\n");
        while (open.size() > 0) {
            Node node = getBestNode(open);
            int pathLength = node.getPath().size();
            closed.add(node);
            if (node.equals(goal)) {
                solution = node;
                break;
            }
            Vector<Node> successors = space.getSuccessors(node);
            for (int i = 0; i < successors.size(); i++) {
                Node successor = successors.get(i);
                int cost = hCost(successor) + pathLength + 1;
                int previousCost;
                previousCost = getPreviousCost(successor);
                boolean inClosed;
                inClosed = closed.contains(successor);
                boolean inOpen = open.contains(successor);

                if (!(inClosed || inOpen) || cost < previousCost) {
                    if (inClosed) closed.remove(successor);
                    if (!inOpen) open.add(successor);
                    successor.cost = cost;
                    successor.parent = node;
                }
            }
        }
        if (solution != null) {
            Vector path = solution.getPath();
            System.out.print("\nSolution found\n");
            printPath(path);
        }
    }

    public static void main(String args[]) {
        // Melakukan pencarian
        new EightPuzzleSearch4().run();
    }
}

//EightPuzzleSpace4//
import java.util.Vector;

public class EightPuzzleSpace4 {
    Node getRoot() {
        int ex[] = {4, 2, 5, 1, 6, 7, 8, 3, 0};
        // the Russell and Norvig eg
        int rn[] = {7, 2, 4, 5, 0, 6, 8, 3, 1};
        return new Node(ex, null);
    }

    Node getGoal() {
        int state[] = {1, 8, 7, 2, 0, 6, 3, 4, 5};
        return new Node(state, null);
    }

    Vector<Node> getSuccessors(Node parent) {
        Vector<Node> successors = new Vector<Node>();
        for (int r = 0; r < 3; r++) {
            for (int c = 0; c < 3; c++) {
                if (parent.state[(r * 3) + c] == 0) {
                    if (r > 0) {
                        successors.add(transformState(r-1, c, r, c, parent));
                    }
                    if (r < 2) {
                        successors.add(transformState(r+1, c, r, c, parent));
                    }
                    if (c > 0) {
                        successors.add(transformState(r, c-1, r, c, parent));
                    }
                    if (c < 2) {
                        successors.add(transformState(r, c+1, r, c, parent));
                    }
                }
            }
        }
        parent.successors = successors;
        return successors;
    }

    Node transformState(int r0, int c0, int r1, int c1, Node parent) {
        int[] s = parent.state;
        int[] newState = {s[0], s[1], s[2], s[3], s[4], s[5], s[6], s[7], s[8]};
        newState[(r1 * 3) + c1] = s[(r0 * 3) + c0];
        newState[(r0 * 3) + c0] = 0;
        return new Node(newState, parent);
    }
}

5.	Ubahlah initial dan goal state dari program dan class-class di atas sehingga bentuk initial dan goal state-nya Gambar 5.11. Kemudian tentukan langkah-langkah mana saja sehingga puzzlenya mencapai goal state.

Jawab:
Setelah mengubah initial dan goat state dari program yang diberikan dengan initial = D B E A F G H C 0  dan goat state = A H G B 0 F C D E. Kita lakukan permisalan seperti berikut:
A = 1
B = 2
C = 3
D = 4
E = 5
F = 6
G = 7
H = 8
Kosong = 0

ditemukan hasil penyelesaian sebagai berikut:
4 2 5 1 0 7 8 3 6
4 2 5 0 1 7 8 3 6
4 2 5 1 0 7 8 6 3
4 2 5 1 4 7 8 6 3
4 2 5 1 7 0 8 6 3
4 2 5 1 7 8 0 6 3
4 2 5 1 0 8 7 6 3
4 2 5 0 1 8 7 6 3
4 2 5 6 0 8 7 1 3
4 2 5 6 8 0 7 1 3
4 2 5 6 8 1 7 0 3
4 2 5 6 8 1 0 7 3
4 2 5 6 8 1 3 7 0
4 2 5 6 8 1 3 0 7
4 2 5 6 1 8 3 0 7
4 2 5 6 1 3 8 0 7
4 2 5 6 1 3 8 7 0

