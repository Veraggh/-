'''Подключение модулей'''
from PyQt5.QtCore import Qt
from PyQt5.QtWidgets import *
import pandas as pd




'''Функции'''
def show_titles_of_notes():
    notes = data['Название_заметки'].values
    list_notes.addItems(notes)


def show_note():
    note_title = list_notes.selectedItems()[0].text()
    note = data[data['Название_заметки'] == note_title].reset_index(drop=True)
    text = note['Текст'][0]
    tags = eval(note['Теги'][0])


    text_note.setText(text)
    list_tags.clear()
    list_tags.addItems(tags)

def add_note () :
    global data
    note_title, click = QInputDialog.getText(win,
                                             'Добавить заметку',
                                             'Название заметки:')
    if click == True and note_title != "":
        new_note = pd.DataFrame ({ 'Название_заметки' : [note_title],
                                  'Текст': ['текст заметки'],
                                  'Теги': [ [] ]})
        
        data = pd.concat([data, new_note], ignore_index=True)
        data.to_csv('data.csv', index=False)
        data = pd.read_csv('data.csv')

        list_notes.clear()
        show_titles_of_notes()

def del_note ():
    global data
    if list_notes.selectedItems():
        note_title = list_notes.selectedItems () [0].text ()
        data = data[data['Название_заметки'] != note_title]
        data.to_csv('data.csv', index=False)

        data = pd.read_csv('data.csv')

        list_notes.clear ()
        list_tags.clear ()
        text_note.clear ()
        show_titles_of_notes()
def save_note():
    global data
    if list_notes.selectedItems():
        note_title = list_notes.selectedItems()[0].text()
        new_text = text_note.toPlainText()

        note = data[data['Название_заметки'] == note_title].reset_index(drop=True)
        note['Текст'][0] = new_text

        del_note()

        data = pd.concat ([data, note], ignore_index=True)
        data.to_csv('data.csv', index=False)
        data = pd.read_csv('data.csv')
        list_notes.clear ()
        show_titles_of_notes()

def add_tag ():
    global data 
    if list_notes.selectedIndexes():
        note_title = list_notes.selectedItems () [0].text ()
        tag =  input_tag.text ()

        note = data[data['Название_заметки'] == note_title].reset_index(drop=True)

        lst = eval (note['Теги'] [0])
        lst.append(tag)
        note['Теги'] [0] = lst

        del_note ()

        dtata = pd.concat ([data, note], ignore_index=True)
        dtata.to_csv('data.csv', index=False)
        data = pd.read_csv('data.csv')
        list_notes.clear()
        list_tags.clear()
        input_tag.clesr()
        show_titles_of_notes()

def del_tag():
    global dataif 
    if list_tags.selectedItems():
        note_title = list_notes.selectedItems() [0]. text()
        tag = list_tags.selectedItems() [0].text()

    note = data[data['Название_заметки'] == note_title].reset_index(drop=True)

    lst = eval(note['Теги'][0])
    lst.remove (tag)
    note['Теги'] [0] = lst
    del_note()

    data = pd.concat([data, note], ignore_index=True)
    data.to_csv('data.csv', index=False)
    data = pd.reada_csv('data.csv')

    list_notes.clear()
    list_tags.clear()
    show_titles_of_notes()

def find():
    global data
    tag = input_tag.text()
    if btn_find_tag.text() == 'Искать заметки по тегу' and tag:
        filter_data = data[data['Теги'].str.contains(tag)]
        data = filter_data
        list_notes.clear()
        list_tags.clear()
        show_titles_of_notes()

        btn_find_tag.setText('Сбросить поиск')

    elif btn_find_tag.text() == 'Сбросить поиск':
        data = pd.read_csv('data.csv')

        input_tag.clear()
        list_tags.clear()
        list_tags.clear()
        show_titles_of_notes()
        btn_find_tag.setText('Искать заметки по тегу')





'''Чтение файла с заметками'''
try:
    data = pd.read_csv("data.csv")
except:
    head_table = {'Название_заметки': ['Test'],
              'Текст': ['Test text'],
              'Теги': [['test tag1', 'test tag2']]}
    data = pd.DataFrame(head_table)
    data.to_csv('data.csv', index=False)
    data = pd.read_csv('data.csv')






'''Создание приложения и окна'''
app = QApplication([])
win = QWidget()






'''Настройка окна'''
win.setWindowTitle('Умные заметки')
win.resize(900, 600)






'''Создание виджетов'''
title_notes = QLabel('Список заметок')
list_notes = QListWidget()
btn_new_note = QPushButton('Создать заметку')
btn_del_note = QPushButton('Удалить заметку')
btn_save_note = QPushButton('Сохранить заметку')
title_tags = QLabel('Список тегов')
list_tags = QListWidget()
input_tag = QLineEdit()
input_tag.setPlaceholderText('Введите тег...')
btn_new_tag = QPushButton('Добавить к заметке')
btn_del_tag = QPushButton('Открепить от заметки')
btn_find_tag = QPushButton('Искать заметки по тегу')
text_note = QTextEdit()






'''Создание шампуров'''
hor_line1 = QHBoxLayout()
hor_line2 = QHBoxLayout()
vert_line_left = QVBoxLayout()
vert_line_right = QVBoxLayout()
hor_line_main = QHBoxLayout()






'''Сборка'''
hor_line1.addWidget(btn_new_note)
hor_line1.addWidget(btn_del_note)
hor_line2.addWidget(btn_new_tag)
hor_line2.addWidget(btn_del_tag)
vert_line_left.addWidget(text_note)
vert_line_right.addWidget(title_notes)
vert_line_right.addWidget(list_notes)
vert_line_right.addLayout(hor_line1)
vert_line_right.addWidget(btn_save_note)
vert_line_right.addWidget(title_tags)
vert_line_right.addWidget(list_tags)
vert_line_right.addWidget(input_tag)
vert_line_right.addLayout(hor_line2)
vert_line_right.addWidget(btn_find_tag)
hor_line_main.addLayout(vert_line_left)
hor_line_main.addLayout(vert_line_right)
win.setLayout(hor_line_main)


'''Подписки и вызовы функций'''
show_titles_of_notes()
list_notes.itemClicked.connect(show_note)
btn_new_note.clicked.connect(add_note)
btn_del_note.clicked.connect(del_note)
btn_save_note.clicked.connect(save_note)
btn_new_tag.clicked.connect(add_tag)
btn_del_tag.clicked.connect(del_tag)
btn_find_tag.clicked.connect(find)

'''Показываем окно'''
win.show()
app.exec_()
