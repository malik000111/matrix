import random
import os

# Clear screen function
def clear_screen():
    os.system('cls' if os.name == 'nt' else 'clear')

# Initialize the game board
def initialize_board(size=7):
    return [[" " for _ in range(size)] for _ in range(size)]

# Display the board
def display_board(board, reveal=False):
    print("   A B C D E F G")  # Column labels
    print("  " + "-" * 15)
    for i, row in enumerate(board):
        line = f"{i + 1} |" + " ".join(row) + "|"
        if not reveal:
            line = line.replace("S", " ")
        print(line)
    print("  " + "-" * 15)

# Place ships randomly on the board
def place_ships(board):
    ships = [(3, 1), (2, 2), (1, 4)]  # (size, count)
    for ship_size, count in ships:
        for _ in range(count):
            while True:
                direction = random.choice(["H", "V"])
                row = random.randint(0, 6)
                col = random.randint(0, 6)
                if can_place_ship(board, row, col, ship_size, direction):
                    place_ship(board, row, col, ship_size, direction)
                    break

# Check if a ship can be placed at a given position
def can_place_ship(board, row, col, size, direction):
    if direction == "H" and col + size > 7:
        return False
    if direction == "V" and row + size > 7:
        return False
    for i in range(size):
        r = row + (i if direction == "V" else 0)
        c = col + (i if direction == "H" else 0)
        if board[r][c] == "S" or any_adjacent(board, r, c):
            return False
    return True

# Place the ship on the board
def place_ship(board, row, col, size, direction):
    for i in range(size):
        r = row + (i if direction == "V" else 0)
        c = col + (i if direction == "H" else 0)
        board[r][c] = "S"

# Check if any adjacent cell has a ship
def any_adjacent(board, row, col):
    for r in range(max(0, row - 1), min(7, row + 2)):
        for c in range(max(0, col - 1), min(7, col + 2)):
            if board[r][c] == "S":
                return True
    return False

# Convert letter and number input to board indices
def parse_coordinates(coord):
    col = ord(coord[0].upper()) - ord("A")
    row = int(coord[1]) - 1
    return row, col

# Check if the shot is valid
def valid_shot(board, row, col):
    return 0 <= row < 7 and 0 <= col < 7 and board[row][col] not in ["X", "O"]

# Play the game
def play_game():
    player_name = input("Enter your name: ")
    leaderboard = {}

    while True:
        clear_screen()
        player_board = initialize_board()
        place_ships(player_board)
        player_shots = initialize_board()
        attempts = 0
        hits = 0
        total_ship_parts = 3 + 2 * 2 + 4 * 1

        while hits < total_ship_parts:
            clear_screen()
            print(f"{player_name}'s Turn")
            display_board(player_shots)

            # Get valid input
            while True:
                shot = input("Enter your shot (e.g., B3): ")
                if len(shot) == 2 and shot[0].upper() in "ABCDEFG" and shot[1].isdigit():
                    row, col = parse_coordinates(shot)
                    if valid_shot(player_shots, row, col):
                        break
                    else:
                        print("Invalid shot. Try again.")
                else:
                    print("Invalid input. Enter a letter and a number (e.g., A1).")

            # Process the shot
            attempts += 1
            if player_board[row][col] == "S":
                player_shots[row][col] = "X"
                hits += 1
                print("Hit!")
            else:
                player_shots[row][col] = "O"
                print("Miss!")

        # Victory
        clear_screen()
        print("Congratulations, you sunk all the ships!")
        print(f"Total shots: {attempts}")
        leaderboard[player_name] = attempts

        # Play again or quit
        play_again = input("Do you want to play again? (yes/no): ").strip().lower()
        if play_again != "yes":
            break

    # Show leaderboard
    clear_screen()
    print("Leaderboard:")
    for name, shots in sorted(leaderboard.items(), key=lambda x: x[1]):
        print(f"{name}: {shots} shots")
    print("Thanks for playing!")

# Start the game
play_game()
