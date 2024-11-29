# matrix
import os, random, string

def clear_screen():
    os.system('cls' if os.name == 'nt' else 'clear')

def display_board(board):
    print("  " + " ".join(string.ascii_uppercase[:7]))
    for i, row in enumerate(board):
        print(f"{i + 1} " + " ".join(row))

def place_ships():
    board = [[" " for _ in range(7)] for _ in range(7)]
    for size, count in [(3, 1), (2, 2), (1, 4)]:
        for _ in range(count):
            while True:
                x, y, d = random.randint(0, 6), random.randint(0, 6), random.choice(["H", "V"])
                if d == "H" and x + size <= 7 and all(board[y][x + i] == " " for i in range(size)):
                    for i in range(size): board[y][x + i] = "S"
                    break
                if d == "V" and y + size <= 7 and all(board[y + i][x] == " " for i in range(size)):
                    for i in range(size): board[y + i][x] = "S"
                    break
    return board

def letter_to_index(l): return string.ascii_uppercase.index(l.upper())

def play_game(name):
    hidden, visible = place_ships(), [[" " for _ in range(7)] for _ in range(7)]
    shots = 0
    while True:
        clear_screen()
        print(f"{name}'s Battle Game")
        display_board(visible)
        print(f"Shots fired: {shots}")
        try:
            move = input("Enter your shot (e.g., B3): ").strip()
            x, y = letter_to_index(move[0]), int(move[1:]) - 1
            if not (0 <= x < 7 and 0 <= y < 7) or visible[y][x] in {"X", "O"}: raise ValueError
        except (ValueError, IndexError):
            print("Invalid move! Try again.")
            continue
        shots += 1
        if hidden[y][x] == "S":
            visible[y][x] = "X"
            hidden[y][x] = " "
            if all(cell != "S" for row in hidden for cell in row):
                clear_screen()
                print(f"Congrats, {name}! You won in {shots} shots!")
                return shots
        else:
            visible[y][x] = "O"

def main():
    scores = {}
    while True:
        clear_screen()
        name = input("Enter your name: ")
        scores[name] = play_game(name)
        if input("Play again? (y/n): ").lower() != "y":
            break
    clear_screen()
    print("Final Scores:")
    for player, shots in sorted(scores.items(), key=lambda x: x[1]):
        print(f"{player}: {shots} shots")
    print("Thanks for playing!")

if __name__ == "__main__":
    main()
