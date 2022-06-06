from flask import Flask, render_template, request
import requests
from datetime import datetime

app = Flask(__name__)


class Pogoda():
    def __init__(self, temperatura, wiatr, opis):
        self.temperatura = temperatura
        self.wiatr = wiatr
        self.opis = opis

    def __str__(self):
        return ('Temperatura: ' + self.temperatura +
                ' Wiatr: ' + self.wiatr +
                ' Opis: ' + self.opis)


@app.route('/', methods=['GET'])
def index():
    return render_template('index.html', current_day=datetime.today().date(),
                           current_time=datetime.now().strftime("%H:%M"))


@app.route('/pogoda/', methods=['POST', 'GET'])
def pogoda():
    miejscowosc = ""
    url = 'https://goweather.herokuapp.com/weather/'
    if request.method == 'POST':
        miejscowosc = request.form['miejscowosc'].capitalize()
        if miejscowosc.isalpha():
            try:
                pogoda = requests.get(url+miejscowosc).json()
                if not [value for key, value in pogoda.items() if value == ''] == ['', '', '']:
                    pogoda_teraz = Pogoda(pogoda['temperature'],
                                          pogoda['wind'], pogoda['description'])
                    lista_prognoz = [(dzien['temperature'], dzien['wind'])
                                     for dzien in pogoda['forecast']]
                    dni = ['Jutro', 'Po jutrze', 'Kolejny dzień']
                    prognoza = {key: value for key, value in zip(dni, lista_prognoz)}
                else:
                    return render_template('zla_nazwa.html')
            except:
                return render_template('error.html')
        else:
            return render_template('zla_nazwa.html')
    return render_template('pogoda.html', miejscowosc=miejscowosc,
                           pogoda_teraz=pogoda_teraz,
                           prognoza=prognoza)


@app.errorhandler(404)
def page_not_found(e):
    return render_template('404.html'), 404


@app.errorhandler(500)
def internal_server_error(e):
    return render_template('500.html'), 500
