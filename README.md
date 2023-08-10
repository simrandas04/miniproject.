# try to align

import random
from tkinter import *
import mysql.connector as mc
from prettytable import PrettyTable
from tkinter import messagebox
from tkVideoPlayer import TkinterVideo
import pygame


con = mc.connect(host = 'localhost', user = 'root',password = 'i know it')
cur = con.cursor()
if con.is_connected():
    print('Your MySQL connection has been established.')
cur.execute('CREATE DATABASE IF NOT EXISTS Hangman;')
cur.execute('USE Hangman')


l1 = {"fruit": ["blueberry", "tangerine","mango"], "vegetable": ["carrot", "potato","onion"], "animal":["cat", "dog", "horse"]}
l2 = {'sport': ['hockey', 'archery', 'swimming'], 'flower': ['marigold', 'petunia','chrysanthemum']}
l3 = {'city': ['quebec', 'sydney', 'norway'], 'gemstone' : ['amethyst', 'sapphire']}


def pick_word(value):
    global hint
    
    if value == "Easy":
        x = []
        for key in l1.keys():
            x.append(key)
        hint = random.choice(x)
        print(hint)
        
        def word_select():
            global word
            word = random.choice(l1[hint])
            print(word)
        word_select()
 
    elif value == "Medium":
        x = []
        for key in l2.keys():
            x.append(key)
        hint = random.choice(x)
        print(hint)
        
        def word_select():
            global word
            word = random.choice(l2[hint])
            print(word)
        word_select()
    
    else:
        x = []
        for key in l3.keys():
            x.append(key)
        hint = random.choice(x)
        print(hint)
        
        def word_select():
            global word
            word = random.choice(l3[hint])
            print(word)
        word_select()



def display_word(word):
    global display
    display = ''
    
    for i in word:
        if i in "aeiou":
            display += i.upper() + '  '
        else:
            display += '__  '


guessed_letters = []

n = 0

def guess_check():
    global canvas
    global n
    global guessed_letters
    
    l = input_letter.get()
    l = l.lower()
    
    
    if l.isalpha() == False:
        error.config(text = "ERROR! ENTER A VALID GUESS")
    
    elif l in word:
        error.config(text = 'Correct guess!', fg= 'green')
        guessed_letters.append(l)
        guesses.config(text = f'Guessed Letters:  {guessed_letters}')
    
    else:
        error.config(text = "Wrong Guess! Try Again", fg = 'red')
        guessed_letters.append(l)
        guesses.config(text = f'Guessed Letters:  {guessed_letters}')
    
        n = n + 1
    
        for i in range(n):
            canvas.itemconfig(circle_list[i], fill = '#FF2A22')
    
        
    
    guess_counter.config(text = "The number of guesses left:  " + str(int(10 - n)))
    input_letter.delete(0,END)
 

    global guesses_word
    guesses_word = ''

    for i in word:
        if i in guessed_letters or i in 'aeiou':
            guesses_word = guesses_word + i.upper() + '  '
        else:
            guesses_word = guesses_word + '__  '
    guess_word.config(text = guesses_word) 

    word2 = ''

    for i in word:
        word2 = word2 + i.upper() + '  '


    if guesses_word == word2 or l == word:
        global score
        score = (10 - n) * 10
        
        guess_word.config(text = word2)
        add_sql()

        forget(user_input, you, guess_word, Guess_letter, input_letter, error, warning, guess_counter, guesses, canvas)
        Result.config(text = "YOU HAVE GUESSED THE WORD!")
        
        score_label = Label(root, text = 'Your score is:  ' + str(score), font = ('Courier', 28))
        score_label.place(x = 580, y=100)
       
        
        
    if n == 7:
        warning.config(text = "YOU HAVE THREE TRIES LEFT!!!")

    elif n == 8:
        warning.config(text = "YOU HAVE TWO TRIES LEFT!!!")

    elif n == 9:
        warning.config(text = "YOU HAVE ONE TRY LEFT!!!")

    elif n == 10:
        score = (10 - n) * 10
        
        add_sql()

        forget(user_input, you, guess_word, Guess_letter, input_letter, error, warning, guess_counter, guesses, canvas)
        Result.config(text = f"YOU DID NOT GUESS THE WORD")
        
        reveal = Label(root, text = f'The word was {word}', font = ('Courier', 30))
        reveal.place(x = 550, y = 120)


        score_label = Label(root, text = 'Your score is:  ' + str(score), font = ('Courier', 28))
        score_label.place(x = 600, y=350)
        
        pygame.mixer.init()

        videocanvas = Canvas(root, height = 50, width = 100)
        videocanvas.place(x = 500, y=500)
        videoplayer = TkinterVideo(master = videocanvas)
        videoplayer.set_size((500, 500))
        videoplayer.load('C:\\Users\\Shreya Soni\\Downloads\\Game Lost.mp4')
        pygame.mixer.music.load('C:\\Users\\Shreya Soni\\Videos\\Captures\\Sad Trombone.mp3')


        videoplayer.pack(expand = True, fill = 'both')
        videoplayer.place(x = 800, y = 500)
        videoplayer.play()
        pygame.mixer.music.play(loops = 0)
        
        #play_again = Button(root, text = 'Play Again', font = ('Courier', 24), 
        #                    command = lambda: [forget(Result, score_label,canvas_textbox, )])
    
        
        
root = Tk()
root.attributes('-fullscreen', True)
#root.geometry("800x600")
root.title("Hangman")
#root['background'] = '#f5f5f5'

hangman_label = Label(root, text = 'HANGMAN', font = ('Courier', 100), fg = '#03468F')
hangman_label.place(x = 480, y = 30)

user_name = Label(root, text="Enter your name: ", font = ('Courier', 28))
user_name.place(x = 400, y = 200)


String_name = StringVar()
e = Entry(root, textvariable = String_name, font = ('Courier', 28), foreground = '#492F7A')
e.place(x = 760, y = 200)
 

def get_name():
    global name
    name = String_name.get()
    
    
def forget(*m_widget):
    for widget in m_widget:
        widget.destroy()
        

def exit_window():
    answer = messagebox.askyesno('Exit', 'Are you sure you want to exit?')
    if answer:
        root.destroy()
    else:
        messagebox.showinfo('Return', 'You will now return to the game.')    



def start():
    root.title("MENU")
    global name
    global label2
    label2 = Label(root, text = "Welcome to the game " + name.upper() + '!', font = ('Courier', 50), fg = '#03468F')    
    global LEVELS
    global Easy
    global Medium
    global Hard
    
    LEVELS = Label(root, text = "LEVELS", font = ('Courier', 36), fg = '#492F7A')
    Easy = Button(root, text = "EASY", font = ('Courier', 24, 'bold'), bg = '#2D3845', fg = 'white', command = lambda: button_click("Easy"))
    Medium = Button(root, text = "MEDIUM", font = ('Courier', 24, 'bold'), bg = '#2D3845', fg = 'white', command = lambda: button_click("Medium"))
    Hard = Button(root, text = "HARD", font = ('Courier', 24, 'bold'), bg = '#2D3845', fg = 'white', command = lambda: button_click("Hard"))
    
    exit_button.place(x = 260, y = 600)
    label2.place(x = 800, y = 50, anchor = CENTER)
    LEVELS.place(x = 680, y = 150)
    Easy.place(x = 720, y = 250)
    Medium.place(x = 700, y = 350)
    Hard.place(x = 720,y = 450)


def Tips():
    str_rules = '''
    1)  The hint of the word to be guessed is displayed on the top.
    2)  All vowels are provided as starting clues.
    3)  You will have ten tries to guess the word.
    4)  Try to guess the word before all circles turn red!'''
    
    Rules_label = Label(root, text = 'RULES:', font = ('Courier', 50), fg = '#604592')
    Rules_label.place(x = 650, y = 30)
    Rules_text_label = Label(root, text = str_rules, font = ('Courier', 24))
    Rules_text_label.place(x = 100, y = 130)
    
    str_tips = '''
    Try starting your guesses with the letters T, N, S, H, R, D and L. These 
    are the most commonly occuring letters
    (from most common to least common), 
    and can increase your odds at winning.'''  
    
    Tips = Label(root, text = "TIPS:", font = ('Courier', 50), fg = '#604592')
    Tips.place(x = 670, y = 360)
    Tips_text_label = Label(root, text = str_tips, font = ('Courier', 24))
    Tips_text_label.place(x = 0, y = 460)
    
    Continue = Button(root, text = 'Start Game!', font = ('Courier', 24), bg = '#E9F9DC',
                      command = lambda: [forget(Rules_label, Rules_text_label, Tips, Tips_text_label, Continue), start()])
    Continue.place(x = 640, y = 700)
    
    exit_button.place(x = 80, y = 700)


def button_click(value):
    exit_button.place(x = 60, y = 750)
    
    global x
    global you
    x = value
    forget(label2,LEVELS,Easy, Medium, Hard)
    pick_word(x)
    you = Label(root, text = "The word is a " + hint.upper(), font = ('Courier', 50), fg = '#03468F')
    you.place(x = 460, y = 30)
    display_word(word)
    
    global guess_word
    global Guess_letter
    guess_word = Label(root, text = display, font = ('Courier', 30, 'bold'))
    guess_word.place(x = 800, y = 180, anchor = CENTER)
    Guess_letter = Label(root, text = "Enter your guess:  ", font = ('Courier', 20))
    Guess_letter.place(x = 350, y = 330)
    
    global input_letter
    input_letter = Entry(root, font = ('Courier', 24))
    input_letter.place(x = 650, y = 330)
    
    global error
    error = Label(root, font = ('Courier', 28))
    error.place(x = 780, y = 430, anchor = CENTER)
    
    global guess_counter
    guess_counter = Label(root, text = "The number of guesses left:  "  + str(10))
    guess_counter.grid(row = 6, column = 0)

    
    global guesses
    guesses = Label(root, font = ('Courier', 24))
    guesses.place(x = 780, y = 500, anchor = CENTER)
    
    global warning
    warning = Label(root)
    warning.grid(row = 8, column = 0)
    
    global Result
    Result = Label(root, font = ('Courier', 40), fg = 'purple')
    Result.place(x = 800, y = 50, anchor = CENTER)
    
    global user_input
    user_input = Button(root, text = "INPUT",font = ('Courier', 24), command = lambda: guess_check())
    user_input.place(x = 1100, y = 315)
    
    global canvas
    canvas = Canvas(root, height = 150, width = 500)
    canvas.place(x = 520, y = 600)
    c1 = canvas.create_oval(5,100,45,140, outline = 'black', fill = '#7EC636', width = 2)
    c2 = canvas.create_oval(55,100,95, 140, outline = 'black', fill = '#7EC636', width = 2)
    c3 = canvas.create_oval(105,100,145,140, outline = 'black', fill = '#7EC636', width = 2)
    c4 = canvas.create_oval(155,100,195, 140, outline = 'black', fill = '#7EC636', width = 2)
    c5 = canvas.create_oval(205,100,245,140, outline = 'black', fill = '#7EC636', width = 2)
    c6 = canvas.create_oval(255,100,295, 140, outline = 'black', fill = '#7EC636', width = 2)
    c7 = canvas.create_oval(305,100,345,140, outline = 'black', fill = '#7EC636', width = 2)
    c8 = canvas.create_oval(355,100,395, 140, outline = 'black', fill = '#7EC636', width = 2)
    c9 = canvas.create_oval(405,100,445,140, outline = 'black', fill = '#7EC636', width = 2)
    c10 = canvas.create_oval(455,100,495, 140, outline = 'black', fill = '#7EC636', width = 2)
    
    global circle_list
    circle_list = [c1, c2, c3, c4, c5, c6, c7, c8, c9, c10]
    

def add_sql():

    global canvas_textbox
    
    create_query = "CREATE TABLE IF NOT EXISTS Score_Board (Player_No INT PRIMARY KEY AUTO_INCREMENT, Name VARCHAR(15), Number_of_Wrong_Guesses INT, Score INT, Guessed_word VARCHAR(15), Difficulty VARCHAR(6));"
    cur.execute(create_query)
    con.commit()
    
    insert_query = "INSERT INTO Score_Board (name, number_of_wrong_guesses, score, guessed_word, difficulty) VALUES('{}',{},{},'{}','{}');".format(name,n,score,word, x)
    cur.execute(insert_query)
    con.commit()
    
    select_query = "SELECT * FROM Score_Board WHERE Name = '{}';".format(name)
    cur.execute(select_query)
    column = cur.column_names
    data = cur.fetchall()
    canvas_textbox = Text(root, height = 6, width = 85, bg = 'black', fg = 'white')
    table = PrettyTable()
    table.field_names = [x for x in column]
    
    for i in data:
        table.add_row(i)
    canvas_textbox.insert(INSERT, table)
    canvas_textbox.place(x = 450, y = 200)
    con.close()

    
def leaderboard():
    exit_button.place(x = 80, y = 700)
    
    select_query = "SELECT * FROM Score_Board ORDER BY Score DESC;"
    cur.execute(select_query)
    column = cur.column_names
    data = cur.fetchall()
    
    t = Text(root, height = 10, width = 100)
    table = PrettyTable()
    table.field_names  = [x for x in column]
    
    for i in data:
        table.add_row(i)
    t.insert(INSERT, table)
    t.grid(row = 11, column = 0)
    
    con.close()
    

button1 = Button(root, text = "Start",font = ('Courier', 24), fg = 'Black', bg = '#E2F5F9',
                 command = lambda: [get_name(), forget(user_name, e, rules, button1, hangman_label, leaderboard_button), start()])
button1.place(x = 900, y = 420)

rules = Button(root, text = "Rules and Tips", font = ('Courier', 24), bg = '#EEE4F1',
               command = lambda: [forget(user_name, e, rules, button1, hangman_label, leaderboard_button), Tips(), get_name()])
rules.place(x = 620, y = 300)

exit_button = Button(root, text = 'Exit', font = ('Courier', 24), bg = '#E2F5F9',width = 6, command = exit_window)
exit_button.place(x = 520 , y = 420)

leaderboard_button = Button(root, text = 'Leaderboard', font = ('Courier', 24), bg = '#EEE4F1',
                            command = lambda: [forget(user_name, e, button1, rules, hangman_label, leaderboard_button), leaderboard()])
leaderboard_button.place(x = 640, y = 540)

root.mainloop()

