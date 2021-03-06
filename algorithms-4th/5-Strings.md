# 5. Strings


## String sorts

### Key-indexed Counting

-   Compute frequency counts
-   Transform counts to indices
-   Distribute the data
-   Copy back

```java
public class KeyIndexedCounting {
    public void sort(String[] a) {
        int N = a.length;
        String[] aux = new String[N];
        int[] count = new int[R+1];

        for (int i < 0; i < N; i++) {
            count[a[i].key() + 1]++;
        }

        for (int r = 0; r < R; r++) {
            count[r+1] = count[r];
        }

        for (int i = 0; i < N; i++) {
            aux[count[a[i].key()]++] = a[i];
        }

        for (int i = 0; i < N; i++) {
            a[i] = aux[i];
        }
    }
}
```

### LSD string sort

Least-significant-digit first string sort.

Consider the string from right to left.

LSD only works for fixed-length strings.

```java
public class LSD {
    pubic staitc void sort(String[] a, int W) {
        int N = a.length;
        int R = 256;
        String[] aux = new String[N];
        for (int d = W-1; d >= 0; d--) {
            int count = new int[R+1];

            for (int i = 0; i < N; i++) {
                count[a[i].charAt(d)+1]++;
            }

            for (int r = 0; r < R; i++) {
                count[r+1] += count[r];
            }

            for (int i = 0; i < N; i++) {
                aux[count[a[i].charAt(d)]++] = a[i];
            }

            for (int i = 0; i < N; i++) {
                a[i] = aux[i];
            }
        }
    }
}
```

### MSD string sort

To implement a general-purpose string sort, where strings are not necessarily all the same length, we consider the characters in left-to-right order.

```java
public class MSD {
    private static int R = 256;
    private static final int M = 15;
    private static String[] aux;

    public static void int charAt(String[] a, int d) {
        if (d < s.length()) return s.charAt(d);
        else return -1;
    }

    public static void sort(String[] a) {
        int N = a.length;
        aux = new String[N];
        sort(a, 0, N-1, 0);
    }

    private static void sort(String[] a, int lo, int hi, int d) {
        if (hi <= lo + M) {
            Insertion.sort(a, lo, hi, d);
            return;
        }

        int[] count = new int[R+2];

        for (int i = lo; i <= hi; i++)
            count[charAt(a[i], d)+2]++;
        for (int r = 0; r < R+1; r++)
            count[r+1] += count[r];
        for (int i = 0; i <= hi; i++)
            aux[count[charAt(a[i], d)+1]++] = a[i];
        for (int i = lo; i <= hi; i++)
            a[i] = aux[i - lo];
        for (int r = 0; r < R; r++)
            sort(a, lo+count[r], lo+count[r+1], d+1);
    }
}

```

> To sort N strings taken from an R-character alphabet, the amount of space needed by MSD string sort is proportional to R times the length of the longest string (plus N), in the worst case.

| algorithm              | stable? | inplace? | running time        |
|---------------------- |------- |-------- |------------------- |
| insertion sort         | yes     | yes      | between N and N^2   |
| quicksort              | no      | yes      | NlogN               |
| mergesort              | yes     | no       | NlogN               |
| 3-way quicksort        | no      | yes      | between N and NlogN |
| LSD string sort        | yes     | no       | NW                  |
| MSD string sort        | yes     | no       | between N and NW    |
| 3-way string quicksort | no      | yes      | between N and NW    |

## Tries

**Search in a trie**

> -   The value at the node corresponding to the last character in the key is not null (as in the searches for shells and she depicted at left above). This result is a search hit—the value associated with the key is the value in the node corre- sponding to its last character.
> 
> -   The value in the node corresponding to the last character in the key is null (as in the search for shell depicted at top right above). This result is a search miss: the key is not in the table.
> 
> -   The search terminated with a null link (as in the search for shore depicted at bottom right above). This result is also a search miss.

```java
public class TrieST<Value> {
    private static int R = 256;
    private Node root;

    private static class Node {
        private Object val;
        private Node[] next = new Node[R];
    }

    public Value get(String key) {
        Node x = get(root, key, 0);
        if (x == null) return null;
        return (Value) x.val;
    }

    private Node get(Node x, String key, int d) {
        if (x == null) return null;
        if (d == key.length()) return x;
        char c = key.charAt(d);
        return get(x.next[c], key, d+1);
    }

    public void put(String key, Value val) {
        root = put(root, key, val, 0);
    }

    private Node put(Node x, String key, Value val, int d) {
        if (x == null) x = new Node();
        if (d == key.lenght()) { x.val = val; return x; }
        char c = key.charAt(d);
        x.next[c] = put(x.next[c], key, val, d+1);
        return x;
    }
}

```

### Ternary search tries (TSTs)

TST avoids the excessive space cost associated with R-way tries

> In a TST, each node has a character, three links, and a value. The three links correspond to keys whose current characters are less than, equal to, or greater than the node’s character.

```java

```

## Substring Search

Brute-force backing up text.

If i and j point to mismatching characters, then we back up both pointers: j to point to the beginning of the pattern and i to correspond to moving the pattern to the right one position

```java
public static int search(String pat, String txt) {
    for (i = 0, j = 0; i < N && j < M; i++) {
        if (txt.charAt(i) == pat.charAt(j)) j++;
        else { i -= j; j = 0; }
    }

    if (j == M) return i - M;
    else return N;
}
```

### KMP

Knuth-Morris-Pratt substring search.

The basic idea:

> Whenever we detect a mismatch, we already know some of the characters in the text. We can take advantage of this information to void backing up the text pointer over all those known characters.

**Backing up the pattern pointer**. We use an array dfs[][] to record how far to back up the pattern pointer j when a mismatch is detected.

**KMP search method**. Once we have computed the dfa[][] array: when i and j point to mismatching characters, set j to dfs[txt.charAt(i)][j] and increment i.

```java
public int search(String txt) {
    int i, j, N = txt.length();
    for (i = 0, j = 0; i < N && j < M)
        j = dfs[txt.charAt(i)][j];
    if (j == M) return i - M;
    else return N;
}
```

**DFA simulation**. A useful way to describe this process is in terms of a deterministic finite-state automaton (DFA).

**Constructing the DFA** Remarkably, to construct the dfa[][] array is to use DFA itself. When we have a mismatch at pat.charAt(j), our interest is in knowing in what state the DFA would be if we were to back up the text index and rescan the text characters that we just saw after shifting to the right one position. (It's like, we have treat the pattern as the text, when we have a mismatch, we want to move to the next position of the text, and use the DFA to find the pattern from there. As we have already construct the DFA before position j, we can use it to match the pattern until j-1)

The crucial detail to the computation is to observe that maintaining the restart position X.

For each j:

-   Copies dfs[][X] to dfa[][j] (for mismatch cases)
-   Sets dfa[pat.charAt(j)][j] to j+1 (for match case)
-   Updates X

```java
public void construct() {
    dfa[pat.charAt(0)][0] = 1;
    for (int X = 0, j = 1; j < M; j++) {
        for (int c = 0; c < R; c++)
            dfa[c][j] = dfa[c][X];
        dfa[pat.charAt(j)][j] = j+1;
        X = dfa[pat.charAt(j)][X];
}
```

```java
public class KMP {
    private String pat;
    private int[][] dfa;

    public KMP(String pat) {
        this.pat = pat;
        int M = pat.length();
        int R = 256;

        dfa = new int[R][M];
        dfa[pat.charAt(0)][0] = 1;
        for (int X = 0, j = 1; j < M; j++) {
            for (int c = 0; c < R; c++)
                dfa[c][j] = dfa[c][X];
            dfa[pat.charAt(j)][j] = j+1;
            X = dfa[pat.charAt(j)][X];
        }
    }

    public int search(String txt) {
        int i, j, N = txt.length(), M = pat.length();
        for (i = 0, j = 0; i < N && j < M; i++)
            j = dfa[txt.charAt(i)][j];

        if (j == M) return i - M;
        else return N;
    }

    public static void main(Stirng[] arg) {
        String pat = args[0];
        String txt = args[1];
        KMP kmp = new KMP(pat);
        StdOut.println("text:     " + txt);
        int offset = kmp.search(txt);
        StdOut.print("pattern: ");
        for (int i = 0; i < offset; i++)
            StdOut.print(" ");
        StdOut.println(pat);
    }
}
```

### Boyer-Moore

### Rabin-Karp

## Regular Expression

**Nonedeterministic finite-state automata**

The characteristic of DFA that makes it easy to simulate is that it is deterministic: each state transition is completely determined by the next character in the text.

To handle regular expressions:

1.  because of or operation, the automaton cannot determine wether or not the pattern could occur at a given point examining just one character
2.  because of closure, it cannot even determine how many characters might need to be examined before a mismatch is discovered.

To be able to guess the next state, we make sure that we check all possible sequences of state transitions, so if there is one that gets to the accept state, we will find it.

So it is nondeterministic.

**Simulation an NFA** Computing multiple-source reachability in a diagraph.

> Determining whether an N-character text string is recognized by the NFA corresponding to an M-character RE takes time proportional to NM in the worst case.

**Building an DFA corresponding to an RE**

-   Concatenation
-   Parenthese
    -   push index of each left parenthesis on the stack.
    -   each time we encounter a right parenthesis, we eventually pop the corresponding left parenthesis from the stack.
-   Closure
    -   after a single character
    -   after a right parenthesis
-   Or expression
    -   one from ( to first character of B
    -   one from | to )

```java
public class NFA {
    private char[] re;
    private Digraph G;
    private int M;

    public NFA(String regexp) {
        Stack<Integer> ops = new Stack<Integer>();
        re = regexp.toCharArray();
        M = re.length;
        G = new Digraph(M+1);

        for (int i = 0; i < M; i++) {
            int lp = i;
            if (re[i] == '(' || re[i] == '|')
                ops.push(i);
            else if (re[i] == ')') {
                int or = ops.pop();
                if (re[or] == '|') {
                    lp = ops.pop();
                    G.addEdge(lp, or+1);
                    G.addEdge(or, i);
                }
                else lp = or;
            }
            if (i < M-1 && re[i+1] == '*') {
                G.addEdge(lp, i+1);
                G.addEdge(i+1, lp);
            }
            if (re[i] == '(' || re[i] == '*' || re[i] == ')')
                G.addEdge(i, i+1);
        }
    }

    public boolean recognizes(String txt) {
        Bag<Integer> pc = new Bag<Integer>();
        DirectedDFS dfs = new DirectedDFS(G, 0);
        for (int v = 0; v < G.V(); v++)
            if (dfs.marked(v)) pc.add(v);

        for (int i = 0; i < txt.length(); i++) {
            Bag<Integer> match = new Bag<Integer>();
            for (int v : pc) {
                if (v < M) {
                    if (re[v] == txt.charAt(i) || re[v] == '.') {
                            match.add(v+1);
                       }
                }
            }
            pc = new Bag<Integer>();
            dfs = new DirectedDFS(G, match);
            for (int v = 0; v < G.V(); v++) {
                if (dfs.marked(v)) pc.add(v);
            }
        }
        for (int v : pc) if (v == M) return true;
        return false;
    }
}

```

## Data Compression

### Huffman Compression

We use fewer characters that appear often than for those appear rarely.

ABRACADABRA! Encoding it in 7-bit ASCII gives:

> 1000001 1000010 1010010 1000001 1000011 1000001 1000100 1000001 1000010 1010010 1000001 0100001

To decodethis bistring, we simply read off 7 bits at a time and convert according to the ASCII coding table.

In this standard code the D, which appears only once, requires the same number of bits as the A, which appears five times.

Huffmann compression is based on the idea that we can save bits by encoding frequently used characters with fewer bits than rarely used characters, thereby lowering the total number of bits used.

**variable-length prefix-free codes**:

Prefix-free is to avoid ambiguity, so that we don't have to use seperator between the encode.

Encode

A with 0, B with 11111, C with 110, D with 100, R with 1110, and ! with 101.

> 011111110011001000111111100101

All prefix-free codes are uniquely decodable.

**Trie representation for prefix-free codes**:

Any trie with M null links defines a prefix-free code for M characters. The general method for finding the optimal prefix-free code is Huffman encoding.

**Trie nodes**

-   Trie Node
-   Expansion
-   Writing and reading the trie
-   Trie construction
-   Compression

```java
public class Huffman {
    private static final int R = 256;
    private static class Node implements Comparable<Node> {
        private char ch;
        private int freq;
        private final Node left, right;

        Node(char ch, int freq, Node left, Node right) {
            this.ch = ch;
            this.freq = freq;
            this.left = left;
            this.right = right;
        }

        public boolean isLeaf() {
            return left == null && right == null;
        }

        public int compareTo(Node that) {
            return this.freq - that.freq;
        }
    }

    public static void expand() {
        Node root = readTrie();
        int N = BinaryStdIn.readInt();

        for (int i = 0; i < N; i++) {
            Node x = root;
            while (!x.lsLeaf()) {
                if (BinaryStdIn.readBoolean())
                    x = x.right;
                else
                    x = x.left;
            }
            BinaryStdOut.write(x.ch, 8);
        }
        BinaryStdOut.close();
    }

    private static Node buildTrie(int[] freq) {
        MinPQ<Node> pq = new MinPQ<Node>();
        for (char c = 0; c < R; c++)
            if (freq[c] > 0)
                pq.insert(new Node(c, freq[c], null, null));

        while (pq.size() > 1) {
            Node x = pq.delMin();
            Node y = pq.delMin();
            Node parent = new Node('\0', x.freq + y.freq, x, y);
            pq.insert(parent);
        }
    }

    private static String[] buildCode(Node root) {
        String[] st = new String[R];
        buildCode(st, root, "");
        return st;
    }

    private static void buildCode(String[] st, Node x, String s) {
        if (x.isLeaf()) {
            st[x.ch] = s;
            return;
        }
        buildCode(st, x.left, s + '0');
        buildCode(st, x.right, s + '1');
    }

    private static void writeTrie(Node x) {
        if (x.isLeaf()) {
            BinaryStdOut.write(true);
            BinaryStdOut.write(x.ch);
            return;
        }
        BinaryStdOut.write(false);
        writeTrie(x.left);
        writeTrie(x.right);
    }

    private static Node readTrie() {
        if (BinaryStdIn.readBoolean())
            return new Node(BinaryStdIn.readChar(), 0, null, null);
        return new Node('\0', )
            }

    public static void compress() {
        String s = BinaryStdIn.readString();
        char[] input = s.toCharArray();

        int[] freq = new int[R];

        for (int i = 0; i < input.length; i++) {
            freq[input[i]]++;
        }

        Node root = buildTrie(freq);

        String[] st= new String[R];
        buildCode(st, root, "");
        writeTrie(root);

        BinaryStdOut.write(input.length);

        for (int i = 0; i < input.length; i++) {
            String code = st[input[i]];
            for (int j = 0; j < code.length(); j++) {
                if (code.charAt(j) == '1')
                    BinaryStdOut.write(true);
                else BinaryStdOut.write(false);
            }
        }
        BinaryStdOut.close();
    }
}
```

### LZW Compression

Progressively learn und update model as you read text.

```java
public class LZW {
    private static final int R = 256;
    private static final int L = 4096;
    private static final int W = 12;

    public static void compress() {
        String input = BinaryStdIn.readString();
        TST<Integer> st = new TST<Integer>();

        for (int i = 0; i < R; i++)
            st.put("" +(char) i, i);
        int code = R+1;
        while (input.length() > 0) {
            String s = st.longestPrefixOf(input);
            BinaryStdOut.write(st.get(s), w);
            int t = s.length();
            if  (t < input.length() && code < L)
                st.put(input.substring(0, t + 1), code++);
            input = input.substring(t);
        }
        BinaryStdOut.write(R, W);
        BinaryStdOut.close();
    }

    public static void expand() {
        String[] st = new String[L];
        int i;
        for (i = 0; i < R; i++)
            st[i] = "" + (char) i;

        st[i++] = " ";

        int codeword = BinaryStdIn.readInt(W);
        String val = st[codeword];
        while (true) {
            BinaryStdOut.write(val);
            codeword = BinaryStdIn.readInt(W);
            if (codeword == R) break;
            String s = st[codeword];
            if (i == codeword)
                s = val + val.charAt(0);
            if (i < L)
                st[i++] = val + s.charAt(0);
            val = s;
        }
        BinaryStdOut.close();
    }
}
```
