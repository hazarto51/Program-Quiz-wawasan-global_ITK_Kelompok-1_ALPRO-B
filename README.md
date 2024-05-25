# Program-Quiz-wawasan-global_ITK_Kelompok-1_ALPRO_B
import sys
import json
from PyQt5.QtWidgets import QApplication, QMainWindow, QLabel, QPushButton, QMessageBox, QLineEdit, QDialog, QVBoxLayout, QRadioButton, QGroupBox
import random

# Fungsi untuk memuat pengguna terdaftar dari file JSON
def load_registered_users():
    try:
        with open("registered_users.json", "r") as file:
            return json.load(file)
    except FileNotFoundError:
        return {}



# Fungsi untuk menyimpan pengguna terdaftar ke file JSON
def save_registered_users(registered_users):
    with open("registered_users.json", "w") as file:
        json.dump(registered_users, file)


# Kelas untuk jendela login
class LoginWindow(QMainWindow):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("Login")
        self.setGeometry(100, 100, 400, 200)

        self.init_ui()

    def init_ui(self):
        self.label_username = QLabel("Username:", self)
        self.label_username.setGeometry(50, 30, 100, 30)

        self.label_password = QLabel("Password:", self)
        self.label_password.setGeometry(50, 70, 100, 30)

        self.input_username = QLineEdit(self)
        self.input_username.setGeometry(150, 30, 200, 30)

        self.input_password = QLineEdit(self)
        self.input_password.setGeometry(150, 70, 200, 30)
        self.input_password.setEchoMode(QLineEdit.Password)

        self.btn_login = QPushButton("Login", self)
        self.btn_login.setGeometry(150, 120, 100, 30)
        self.btn_login.clicked.connect(self.login)

        self.btn_register = QPushButton("Register", self)
        self.btn_register.setGeometry(260, 120, 100, 30)
        self.btn_register.clicked.connect(self.open_register_window)

    def login(self):
        username = self.input_username.text()
        password = self.input_password.text()

        if not username or not password:
            QMessageBox.warning(self, "Peringatan", "Username dan password harus diisi")
            return

        if username in registered_users and registered_users[username] == password:
            self.open_lobby_quiz_window()
            self.close()
        else:
            QMessageBox.warning(self, "Peringatan", "Login gagal, username atau password salah")

    def open_lobby_quiz_window(self):
        self.lobby_quiz_window = LobbyQuizWindow()
        self.lobby_quiz_window.show()

    def open_register_window(self):
        self.register_window = RegisterWindow(self)
        self.register_window.show()


# Kelas untuk jendela registrasi
class RegisterWindow(QDialog):
    def __init__(self, parent=None):
        super().__init__(parent)
        self.setWindowTitle("Register")
        self.setGeometry(100, 100, 400, 200)

        self.init_ui()

    def init_ui(self):
        self.label_username = QLabel("Username:", self)
        self.label_username.setGeometry(50, 30, 100, 30)

        self.label_password = QLabel("Password:", self)
        self.label_password.setGeometry(50, 70, 100, 30)

        self.input_username = QLineEdit(self)
        self.input_username.setGeometry(150, 30, 200, 30)

        self.input_password = QLineEdit(self)
        self.input_password.setGeometry(150, 70, 200, 30)
        self.input_password.setEchoMode(QLineEdit.Password)

        self.btn_register = QPushButton("Register", self)
        self.btn_register.setGeometry(150, 120, 100, 30)
        self.btn_register.clicked.connect(self.register)

    def register(self):
        username = self.input_username.text()
        password = self.input_password.text()

        if username not in registered_users:
            registered_users[username] = password
            save_registered_users(registered_users)  # Simpan pengguna terdaftar
            QMessageBox.information(self, "Informasi", "Registrasi berhasil.")
        else:
            QMessageBox.warning(self, "Peringatan", "Username sudah terdaftar.")

# Fungsi untuk menghasilkan pertanyaan untuk setiap tingkat kesulitan
def generate_questions():
    easy_questions = [
        QuizQuestion("Siapakah penemu teleskop?", ["A. Galileo Galilei", "B. Isaac Newton", "C. Albert Einstein", "D. Johannes Kepler"], "A"),
        QuizQuestion("Siapkah tokoh terkenal sebagai pendiri kekaisaran romawi pada abad ke-8 SM ?", ["A. julius caesar", "B. Augustus", "C. Romulus", "D. Constantine"], "C"),
        QuizQuestion("Siapa presiden pertama Amerika Serikat?", ["A. Thomas Jefferson", "B. George Washington", "C. Abraham Lincoln", "D. John Adams"], "B"),
        QuizQuestion("Siapakah nama presiden ke-3 indonesia ?", ["A. Soekarno", "B. B.J Habiebie", "C. Gusdur", "D. Jokowi"], "B"),
        QuizQuestion("Kapan perang dunia ke 2 dimulai ?", ["A. 1945", "B. 1939", "C. 1938", "D. 1940"], "B")
    ]

    medium_questions = [
        QuizQuestion("Restorasi Meiji dimulai pada tahun berapa?", ["A. 1603", "B. 1707", "C. 1868", "D. 1945"], "C"),
        QuizQuestion("Apa nama konstitusi Jepang yang diperkenalkan setelah Perang Dunia II?", ["A. Konstitusi Meiji", "B. Konstitusi Shogun", "C. Konstitusi Showa", "D. Konstitusi Heisei"], "C"),
        QuizQuestion("Siapa pendiri Dinasti Ming?", ["A. Kublai Khan", "B.  Zhu Yuanzhang", "C. Liu Bang", "D. Tang Taizong"], "B"),
        QuizQuestion("Siapa ratu Inggris yang terkenal karena tidak pernah menikah dan dikenal sebagai Ratu Perawan ?", ["A. Ratu Victoria", "B. Ratu Mary I", "C. Ratu Elizabeth I", "D. Ratu Anne"], "C"),
        QuizQuestion("Kapankah Kemerdekaan Indonesia diproklamasikan?", ["A. 17 Agustus 1945", "B. 19 Agustus 1945", "C. 15 Agustus 1945", "D. 20 Agustus 1945"], "A")
    ]

    hard_questions = [
        QuizQuestion("Peristiwa G30S/PKI terjadi pada tahun berapa?", ["A. 1999", "B. 1987", "C. 1945", "D. 1965"], "D"),
        QuizQuestion("Siapa raja Inggris yang terkenal dengan enam istrinya?", ["A. Henry VII", "B.  Henry VIII", "C. Richard III", "D. Edward VI"], "B"),
        QuizQuestion("Pada tahun berapa Magna Carta ditandatangani ?", ["A. 1215", "B. 1066", "C. 1011", "D. 1256"], "A"),
        QuizQuestion("Revolusi Glorious terjadi pada tahun berapa ?", ["A. 1900", "B. 1270", "C. 1688", "D. 1789"], "C"),
        QuizQuestion("Siapa pemimpin Uni Soviet setelah kematian Lenin ?", ["A. Joseph Stalin", "B.  Leon Trotsky", "C. Nikita Khrushchev", "D. Mikhail Gorbachev"], "A")
    ]

    return {"Easy": easy_questions, "Medium": medium_questions, "Hard": hard_questions}

# Kelas untuk mewakili pertanyaan kuis
class QuizQuestion:
    def __init__(self, question, options, correct_answer):
        self.question = question
        self.options = options
        self.correct_answer = correct_answer

# Kelas untuk jendela kuis
class QuizWindow(QDialog):
    def __init__(self, difficulty, questions):
        super().__init__()
        self.setWindowTitle(f"Quiz {difficulty}")
        self.setGeometry(100, 100, 500, 300)
        self.questions = questions
        self.current_question_index = 0
        self.correct_answers = 0
        self.incorrect_asnwer = 0
        self.difficulty = difficulty
        self.init_ui()

    def init_ui(self):
        self.label_question = QLabel(self.questions[self.current_question_index].question, self)

        self.radio_buttons = []
        self.radio_group = QGroupBox()
        self.layout = QVBoxLayout()
        self.create_radio_buttons()
        self.radio_group.setLayout(self.layout)

        self.btn_next = QPushButton("Next", self)
        self.btn_next.clicked.connect(self.next_question)

        main_layout = QVBoxLayout()
        main_layout.addWidget(self.label_question)
        main_layout.addWidget(self.radio_group)
        main_layout.addWidget(self.btn_next)

        self.setLayout(main_layout)

    def create_radio_buttons(self):
        for option in self.questions[self.current_question_index].options:
            radio_button = QRadioButton(option, self)
            self.layout.addWidget(radio_button)
            self.radio_buttons.append(radio_button)

    def next_question(self):
        selected_option = None
        for i, radio_button in enumerate(self.radio_buttons):
            if radio_button.isChecked():
                selected_option = chr(65 + i)  # Convert index to A, B, C, D
                break

        correct_answer = self.questions[self.current_question_index].correct_answer
        if selected_option == correct_answer:
            self.correct_answers += 1
        else:
            self.incorrect_asnwer += 1
            if self.difficulty == "Easy" and self.incorrect_asnwer > 2:
                QMessageBox.information(self, "Informasi", "Kamu salah")
                self.close()
            if self.difficulty == "Medium" and self.incorrect_asnwer >= 1:
                QMessageBox.information(self, "Informasi", "Kamu salah")
                self.close()
            if self.difficulty == "Hard" and self.incorrect_asnwer == 1:
                QMessageBox.information(self, "Informasi", "Kamu salah")
                self.close()
        self.current_question_index += 1

        if self.current_question_index < len(self.questions):
            self.label_question.setText(self.questions[self.current_question_index].question)
            self.clear_radio_buttons()
            self.create_radio_buttons()
        else:
            QMessageBox.information(self, "Quiz Finished", f"You got {self.correct_answers} out of {len(self.questions)} questions correct.")
            self.close()

    def clear_radio_buttons(self):
        for radio_button in self.radio_buttons:
            self.layout.removeWidget(radio_button)
            radio_button.deleteLater()
        self.radio_buttons.clear()


# Kelas untuk jendela lobby kuis
class LobbyQuizWindow(QMainWindow):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("Lobby Quiz Sejarah")
        self.setGeometry(100, 100, 400, 200)

        self.init_ui()

    def init_ui(self):
        self.label_difficulty = QLabel("Pilih Level Kesulitan:", self)
        self.label_difficulty.setGeometry(50, 30, 150, 30)

        self.btn_easy = QPushButton("Easy", self)
        self.btn_easy.setGeometry(50, 70, 100, 40)
        self.btn_easy.setStyleSheet("background-color: lightgreen;")
        self.btn_easy.clicked.connect(self.start_easy_quiz)

        self.btn_medium = QPushButton("Medium", self)
        self.btn_medium.setGeometry(170, 70, 100, 40)
        self.btn_medium.setStyleSheet("background-color: lightblue;")
        self.btn_medium.clicked.connect(self.start_medium_quiz)

        self.btn_hard = QPushButton("Hard", self)
        self.btn_hard.setGeometry(290, 70, 100, 40)
        self.btn_hard.setStyleSheet("background-color: lightcoral;")
        self.btn_hard.clicked.connect(self.start_hard_quiz)

        self.btn_exit = QPushButton("Exit", self)
        self.btn_exit.setGeometry(150, 130, 100, 40)
        self.btn_exit.setStyleSheet("background-color: lightgrey;")
        self.btn_exit.clicked.connect(self.exit_application)

    def start_easy_quiz(self):
        self.start_quiz("Easy")

    def start_medium_quiz(self):
        self.start_quiz("Medium")

    def start_hard_quiz(self):
        self.start_quiz("Hard")

    def start_quiz(self, difficulty):
        questions = generate_questions()[difficulty]
        random.shuffle(questions)
        self.quiz_window = QuizWindow(difficulty, questions)
        self.quiz_window.show()

    # Kelas untuk jendela kuis
    class QuizWindow(QDialog):
        def __init__(self, difficulty, questions):
            super().__init__()
            self.setWindowTitle(f"Quiz {difficulty}")
            self.setGeometry(100, 100, 500, 300)
            self.questions = questions
            self.current_question_index = 0
            self.correct_answers = 0
            self.mistakes = 0
            self.max_mistakes = 2 if difficulty == "Easy" else 1

            self.init_ui()

        def init_ui(self):
            self.label_question = QLabel(self.questions[self.current_question_index].question, self)

            self.radio_buttons = []
            self.radio_group = QGroupBox()
            self.layout = QVBoxLayout()
            self.create_radio_buttons()
            self.radio_group.setLayout(self.layout)

            self.btn_next = QPushButton("Next", self)
            self.btn_next.clicked.connect(self.next_question)

            main_layout = QVBoxLayout()
            main_layout.addWidget(self.label_question)
            main_layout.addWidget(self.radio_group)
            main_layout.addWidget(self.btn_next)

            self.setLayout(main_layout)

        def create_radio_buttons(self):
            for option in self.questions[self.current_question_index].options:
                radio_button = QRadioButton(option, self)
                self.layout.addWidget(radio_button)
                self.radio_buttons.append(radio_button)

        def next_question(self):
            selected_option = None
            for i, radio_button in enumerate(self.radio_buttons):
                if radio_button.isChecked():
                    selected_option = chr(65 + i)  # Convert index to A, B, C, D
                    break

            correct_answer = self.questions[self.current_question_index].correct_answer
            if selected_option == correct_answer:
                self.correct_answers += 1
            else:
                self.mistakes += 1

            self.current_question_index += 1

            if self.mistakes > self.max_mistakes:
                QMessageBox.warning(self, "Quiz Failed",
                                    "Anda telah melebihi jumlah kesalahan yang diizinkan. Kembali ke lobby.")
                self.close()
            elif self.current_question_index < len(self.questions):
                self.label_question.setText(self.questions[self.current_question_index].question)
                self.clear_radio_buttons()
                self.create_radio_buttons()
            else:
                QMessageBox.information(self, "Quiz Finished",
                                        f"You got {self.correct_answers} out of {len(self.questions)} questions correct.")
                self.close()

        def clear_radio_buttons(self):
            for radio_button in self.radio_buttons:
                self.layout.removeWidget(radio_button)
                radio_button.deleteLater()
            self.radio_buttons.clear()

    def exit_application(self):
        QApplication.quit()

def main():
    global registered_users
    registered_users = load_registered_users()

    app = QApplication(sys.argv)
    login_window = LoginWindow()
    login_window.show()
    sys.exit(app.exec_())

if __name__ == "__main__":
    main()

