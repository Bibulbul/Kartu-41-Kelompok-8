# Kartu-41-Kelompok-8
#The Final Project of Group 8 (41 Cards Game)
import random

class Card:
    def __init__(self, suit, value):
        self.suit = suit
        self.value = value

    def __repr__(self):
        return f"{self.value} of {self.suit}"

    def get_value(self):
        if self.value == 'Ace':
            return 11
        elif self.value in ['Jack', 'Queen', 'King']:
            return 10
        return self.value


class Deck:
    suits = ['Hearts', 'Diamonds', 'Clubs', 'Spades']
    values = ['Ace'] + list(range(2, 11)) + ['Jack', 'Queen', 'King']

    def __init__(self):
        self.cards = [Card(suit, value) for suit in self.suits for value in self.values]
        random.shuffle(self.cards)

    def draw_card(self):
        return self.cards.pop() if self.cards else None


class Player:
    def __init__(self, name, is_human=False):
        self.name = name
        self.hand = []
        self.is_human = is_human

    def draw_card(self, deck, pile):
        if self.is_human:
            choice = input(f"\n{self.name}, draw from (1) Stock or (2) Pile? ")
            if choice == '1':
                return deck.draw_card()
            elif choice == '2' and pile:
                return pile.pop()
        else:
            return deck.draw_card() or pile.pop()

    def discard_card(self, pile):
        if self.is_human:
            print(f"Your hand: {self.hand}")
            index = int(input("Choose a card to discard (0-3): "))
            card = self.hand.pop(index)
        else:
            card = self.hand.pop(0)  # Computer discards the first card
        pile.append(card)
        print(f"{self.name} discarded: {card}")

    def calculate_score(self):
        suits = {'Hearts': 0, 'Diamonds': 0, 'Clubs': 0, 'Spades': 0}
        for card in self.hand:
            suits[card.suit] += card.get_value()
        
        main_suit = max(suits, key=suits.get)
        score = suits[main_suit]
        for suit, value in suits.items():
            if suit != main_suit:
                score -= value
        return score

    def has_same_suit(self):
        return all(card.suit == self.hand[0].suit for card in self.hand)


def play_game():
    print("Welcome to the 41 Card Game!\n")
    deck = Deck()
    pile = []

    players = [Player(f"Computer {i+1}") for i in range(3)]
    players.append(Player("You", is_human=True))  # Human player

    # Membagikan kartu
    for _ in range(4):
        for player in players:
            player.hand.append(deck.draw_card())

    print("Cards are dealt! The game begins.\n")

    game_over = False
    while not game_over:
        for player in players:
            print(f"\n{player.name}'s turn:")
            if player.is_human:
                print(f"Your hand: {player.hand}")
            else:
                print(f"{player.name} is thinking...")

            # Mengambil kartu
            card = player.draw_card(deck, pile)
            if card:
                print(f"{player.name} drew: {card}")
                player.hand.append(card)
            else:
                print("No cards left in the stock.")

            # Membuang kartu
            player.discard_card(pile)

            # Mengecek kondisi menang
            if player.has_same_suit() or player.calculate_score() == 41:
                print(f"\n🎉 {player.name} ends the game! 🎉")
                game_over = True
                break

            if not deck.cards:
                print("\nNo more cards in the stock. The game ends.")
                game_over = True
                break

    # Penilaian akhir
    print("\n--- Final Scores ---")
    scores = []
    for player in players:
        score = player.calculate_score()
        print(f"{player.name}: {score} points")
        scores.append((score, min(card.get_value() for card in player.hand), player.name))

    # Menentukan pemenang
    scores.sort(reverse=True, key=lambda x: (x[0], -x[1]))
    winner = scores[0]
    print(f"\n🏆 The winner is {winner[2]} with {winner[0]} points! 🏆")


if __name__ == "__main__":
    play_game()
