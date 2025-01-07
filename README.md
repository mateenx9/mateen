import random
import time
import sys


# Base class for all animals
class Animal:
    def __init__(self, name, species, hunger=0, happiness=100):
        self.name = name
        self.species = species
        self.hunger = hunger
        self.happiness = happiness

    def feed(self, food_amount):
        self.hunger -= food_amount
        self.happiness += food_amount * 0.2
        if self.hunger < 0:
            self.hunger = 0
        if self.happiness > 100:
            self.happiness = 100
        print(f"{self.name} has been fed. Hunger: {self.hunger}, Happiness: {self.happiness}")

    def play(self):
        self.happiness += 10
        if self.happiness > 100:
            self.happiness = 100
        print(f"{self.name} is playing! Happiness: {self.happiness}")

    def sleep(self):
        self.happiness += 5
        self.hunger += 5
        if self.happiness > 100:
            self.happiness = 100
        if self.hunger > 100:
            self.hunger = 100
        print(f"{self.name} is sleeping. Hunger: {self.hunger}, Happiness: {self.happiness}")


# Specific Animal Classes
class Dog(Animal):
    def __init__(self, name):
        super().__init__(name, species="Dog")


class Cat(Animal):
    def __init__(self, name):
        super().__init__(name, species="Cat")


class Bird(Animal):
    def __init__(self, name):
        super().__init__(name, species="Bird")


# Virtual Store Class
class AnimalStore:
    def __init__(self):
        self.available_animals = {
            "dog": Dog("Buddy"),
            "cat": Cat("Whiskers"),
            "bird": Bird("Tweety")
        }

    def show_animals(self):
        print("Available animals in the store:")
        for idx, animal in enumerate(self.available_animals.values(), 1):
            print(f"{idx}. {animal.species} - {animal.name}")

    def sell_animal(self, choice):
        animal_names = list(self.available_animals.keys())
        animal_choice = animal_names[choice - 1]
        animal = self.available_animals.pop(animal_choice)
        print(f"You adopted a {animal.species}: {animal.name}")
        return animal


# Player class to interact with the virtual world
class Player:
    def __init__(self, name):
        self.name = name
        self.animals = []
        self.money = 100

    def adopt_animal(self, animal):
        self.animals.append(animal)
        print(f"{self.name} has adopted a new {animal.species}: {animal.name}")

    def feed_all(self, food_amount):
        for animal in self.animals:
            animal.feed(food_amount)

    def play_with_all(self):
        for animal in self.animals:
            animal.play()

    def sleep_all(self):
        for animal in self.animals:
            animal.sleep()


# Game Menu System
class Game:
    def __init__(self):
        self.player = None
        self.store = AnimalStore()

    def start_game(self):
        print("Welcome to Virtual Pet World!")
        player_name = input("What is your name, player? ")
        self.player = Player(player_name)
        self.main_menu()

    def main_menu(self):
        while True:
            print(f"\n{self.player.name}'s Pet World")
            print("1. View your animals")
            print("2. Visit Animal Store")
            print("3. Feed all animals")
            print("4. Play with all animals")
            print("5. Let all animals sleep")
            print("6. Exit Game")

            choice = input("Choose an option: ")

            if choice == "1":
                self.view_animals()
            elif choice == "2":
                self.visit_animal_store()
            elif choice == "3":
                self.feed_all_animals()
            elif choice == "4":
                self.play_with_all_animals()
            elif choice == "5":
                self.sleep_all_animals()
            elif choice == "6":
                self.exit_game()
                break
            else:
                print("Invalid choice, please try again.")

    def view_animals(self):
        if not self.player.animals:
            print(f"{self.player.name}, you don't have any animals yet!")
            return
        print(f"\n{self.player.name}'s Animals:")
        for idx, animal in enumerate(self.player.animals, 1):
            print(f"{idx}. {animal.species} - {animal.name}, Hunger: {animal.hunger}, Happiness: {animal.happiness}")

    def visit_animal_store(self):
        print("\nWelcome to the Animal Store!")
        self.store.show_animals()
        choice = input("Enter the number of the animal you want to adopt, or 'q' to return: ")

        if choice == 'q':
            return

        if choice.isdigit():
            choice = int(choice)
            if 1 <= choice <= len(self.store.available_animals):
                animal = self.store.sell_animal(choice)
                self.player.adopt_animal(animal)
            else:
                print("Invalid choice, returning to the main menu.")
        else:
            print("Invalid input, returning to the main menu.")

    def feed_all_animals(self):
        if not self.player.animals:
            print("You don't have any animals to feed!")
            return

        food_amount = int(input("How much food do you want to give to each animal? "))
        self.player.feed_all(food_amount)

    def play_with_all_animals(self):
        if not self.player.animals:
            print("You don't have any animals to play with!")
            return

        self.player.play_with_all()

    def sleep_all_animals(self):
        if not self.player.animals:
            print("You don't have any animals to sleep!")
            return

        self.player.sleep_all()

    def exit_game(self):
        print(f"Goodbye {self.player.name}! Thanks for playing the Virtual Pet World!")
        sys.exit()


# Main Game Loop
if __name__ == "__main__":
    game = Game()
    game.start_game()



  
