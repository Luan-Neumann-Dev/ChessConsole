# Chess Console
 
> A fully playable two-player chess game running in the terminal — built in C# with complete rule enforcement, special moves, and check/checkmate detection.
 
[![C#](https://img.shields.io/badge/C%23-239120?style=for-the-badge&logo=csharp&logoColor=white)](https://learn.microsoft.com/en-us/dotnet/csharp/)
[![.NET](https://img.shields.io/badge/.NET-512BD4?style=for-the-badge&logo=dotnet&logoColor=white)](https://dotnet.microsoft.com/)

![Project Preview](https://github.com/Luan-Neumann-Dev/ChessConsole/assets/155394874/39f94080-2416-4e3e-9cc3-ad4c44dc0afd)
![Project Preview](https://github.com/Luan-Neumann-Dev/ChessConsole/assets/155394874/311d7bd1-b146-415c-a494-55b073aa1063)

---
 
## 🎯 About
 
Chess Console is a fully functional two-player chess game that runs entirely in the terminal. It enforces all standard chess rules — including piece-specific movement, check detection, and checkmate — and highlights valid moves on the board when a piece is selected.
 
The project is structured across two namespaces: `tabuleiro` handles the generic board engine (pieces, positions, and board state), while `xadrez` builds the chess-specific layer on top of it (game rules, special moves, and each piece's movement logic). This separation makes the board engine reusable and the chess logic clean and isolated.
 
## ✨ Key Features
 
- ♟️ **Full Piece Movement** - All six piece types implemented with correct movement rules (King, Queen, Rook, Bishop, Knight, Pawn)
- 🏰 **Special Moves** - Castling (both kingside and queenside), En Passant, and Pawn Promotion fully implemented
- ✅ **Check & Checkmate Detection** - Every move is validated against self-check; checkmate is verified by brute-forcing all remaining legal moves
- 🎯 **Move Highlighting** - Valid destination squares are highlighted in the terminal when a piece is selected
- 🗂️ **Captured Pieces Display** - Both players' captured pieces are shown on screen each turn
- 🔄 **Turn Management** - Alternating turns with current player and turn counter displayed at all times
 
## 🛠️ Tech Stack
 
**Language:**
- C# — Core language for all game logic, OOP structure, and console rendering
 
**Framework & Runtime:**
- .NET — Runtime and build tooling
 
**Design Patterns & Concepts:**
- OOP with abstract classes, inheritance, and polymorphism
- Namespace separation for layered architecture (`tabuleiro` / `xadrez`)
- `HashSet<Peca>` for efficient piece tracking and set operations
 
## 🚀 Quick Start
 
### Prerequisites
 
- [.NET SDK](https://dotnet.microsoft.com/download) installed
 
```bash
# Clone the repository
git clone https://github.com/Luan-Neumann-Dev/chess-console.git
 
# Navigate to project
cd chess-console
 
# Run the project
dotnet run
```
 
### How to Play
 
- Enter moves in chess notation: origin square first, then destination (e.g., `e2` → `e4`)
- Valid moves are highlighted in dark gray on the board
- The game detects check automatically and prevents illegal moves
- The game ends when checkmate is reached
 
## 📁 Project Structure
 
```
chess-console/
├── Program.cs                      # Entry point — game loop and input handling
├── Tela.cs                         # Console rendering — board, pieces, captured sets
├── tabuleiro/
│   ├── Tabuleiro.cs                # Generic 8x8 board — piece placement and retrieval
│   ├── Peca.cs                     # Abstract base class for all pieces
│   ├── Posicao.cs                  # Row/column coordinate model
│   ├── Cor.cs                      # Enum for piece color (White / Black)
│   └── TabuleiroException.cs       # Custom exception for invalid board operations
└── xadrez/
    ├── PartidaDeXadrez.cs          # Core game logic — moves, check, checkmate, special moves
    ├── PosicaoXadrez.cs            # Chess notation position (e.g., e4) to matrix coordinate
    ├── Rei.cs                      # King — movement + castling logic
    ├── Dama.cs                     # Queen
    ├── Torre.cs                    # Rook
    ├── Bispo.cs                    # Bishop
    ├── Cavalo.cs                   # Knight
    └── Peao.cs                     # Pawn — movement + en passant + promotion
```
 
## 💡 Technical Highlights
 
### Checkmate by Brute Force
Checkmate detection works by iterating over every piece in play for the threatened color, generating all its possible moves, and simulating each one. If no move exists that gets the king out of check, it's checkmate. Crucially, each simulated move is immediately undone — `desfazMovimento` restores the exact board state, including piece move counters.
 
```csharp
public bool testeXequemate(Cor cor)
{
    if (!estaEmXeque(cor)) return false;
 
    foreach (Peca x in pecasEmJogo(cor))
    {
        bool[,] mat = x.movimentosPossiveis();
        for (int i = 0; i < tab.linhas; i++)
        {
            for (int j = 0; j < tab.colunas; j++)
            {
                if (mat[i, j])
                {
                    Peca pecaCapturada = executaMovimento(x.posicao, new Posicao(i, j));
                    bool emXeque = estaEmXeque(cor);
                    desfazMovimento(x.posicao, new Posicao(i, j), pecaCapturada);
                    if (!emXeque) return false;
                }
            }
        }
    }
    return true;
}
```
 
### Abstract Piece with Polymorphic Movement
All pieces extend the abstract `Peca` class and override a single method — `movimentosPossiveis()` — which returns a `bool[,]` matrix representing valid target squares. This makes adding new piece types trivial and keeps the game engine agnostic to piece-specific rules.
 
```csharp
public abstract class Peca
{
    public abstract bool[,] movimentosPossiveis();
 
    public bool movimentoPossivel(Posicao pos)
    {
        return movimentosPossiveis()[pos.linha, pos.coluna];
    }
}
```
 
## 📚 What I Learned
 
**Technical Skills:**
- Applying OOP principles in C# — abstract classes, inheritance, method overriding, and polymorphism in a real domain problem
- Designing a layered architecture with namespace separation between a generic engine and domain-specific rules
- Implementing stateful game logic with move counters per piece to support castling and en passant conditions
- Using `HashSet<T>` for efficient piece set management and set difference operations
 
**Best Practices:**
- Separating rendering logic (`Tela.cs`) from game state (`PartidaDeXadrez.cs`) — clear Single Responsibility
- Using a custom exception class (`TabuleiroException`) to handle domain-specific errors cleanly
- Always undoing simulated moves to keep board state consistent during lookahead checks
 
## 🗺️ Roadmap
 
- [ ] Add a simple AI opponent (minimax with alpha-beta pruning)
- [ ] Implement draw conditions (stalemate, threefold repetition, 50-move rule)
- [ ] Add a graphical UI (WinForms, Avalonia, or a web frontend)
- [ ] Export and import games in PGN format
- [ ] Add move history display during the game
 
## 📝 Notes
 
- This is a **console-based educational project** focused on OOP design and game logic
- Two human players share the same keyboard — no AI opponent yet
- All standard chess rules are enforced except draw conditions
 
## 📄 License
 
MIT License - see LICENSE for details
 
## 👤 Author
 
**Luan Neumann**
 
- LinkedIn: [Luan-Neumann-Dev](https://www.linkedin.com/in/luan-henrique-neumann-dev/)
- GitHub: [@luan-neumann-dev](https://github.com/Luan-Neumann-Dev)
 
---
 
⭐ Found this helpful? Give it a star!
