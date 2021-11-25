# API para IQOption
[![Status da compilação](https://travis-ci.org/n1nj4z33/iqoptionapi.svg?branch=master)](https://travis-ci.org/n1nj4z33/iqoptionapi)
[![Status da documentação](https://readthedocs.org/projects/iqoptionapi/badge/?version=latest)](http://iqoptionapi.readthedocs.io/?badge=latest)
[![Versão do PyPI](https://badge.fury.io/py/iqoptionapi.svg)](https://badge.fury.io/py/iqoptionapi)

Um empacotador python amigável em torno da [IQ Option API](https://iqoption.com).

Informações sobre o uso básico que você pode encontrar em [Wiki](https://github.com/n1nj4z33/iqoptionapi/wiki)

Documentação do Sphinx gerada automaticamente, você pode encontrar [aqui](http://iqoptionapi.readthedocs.io/).

### Instalação
```
instalação do pip --upgrade git+https://github.com/victorratts13/iqoptionapi.git@master
```
Referência para a modificação acima: [StackoverFlow](https://stackoverflow.com/questions/51039974/error-while-calling-function-in-python3-5)

### Um Pequeno exemplo
```python
    from iqoptionapi.api import IQOptionAPI
    import datetime
    import time
    import logging
    logging.basicConfig(format='%(asctime)s %(message)s')

    api = IQOptionAPI("iqoption.com", "email", "Password")

    api.connect()
    time.sleep(0.5)
    print 'Your current blance is: {:.2f}'.format(api.profile.balance)

    #Como obter dados de velas: o 1º param é o ativo ou mais
    #segundo é o tamanho do intervalo das velas (atualmente 60 segundos)
    #e o terceiro é a quantidade de velas que gostaríamos de ter
    api.getcandles(1, 60, 25)
    #de alguma forma, precisamos dessa espera. Não me pergunte o porquê. Caso contrário, não temos dados
    time.sleep(0.25)
    data = api.candles.candles_data
    for candles in data:
        st = datetime.datetime.fromtimestamp(candles[0]+60).strftime('%Y-$')
        '''
        candles data
        0th entry: timestamp of candle
        1st entry: where candles starts in the interval
        2nd entry: where candles finishes in the interval
        3rd entry: upper wick
        4th entry: lower wick
        '''
        print st, candles

    #vamos fazer algumas compras: D
    while api.timesync.server_datetime.second != 48:
        print api.timesync.server_datetime.second
    #The first one is the money you want to set
    #2nd is the asset. Check constants.ASSETS for the values
    #3rd is the which mode. Turbo is for binary. Don't ask me why
    #4th is either call or put
    api.buy(1, 1, "turbo", "call")
    time.sleep(0.5)
```

Verifique se uma negociação foi ganha ou perdida:
Você pode obter o resultado usando a variável 'vitória', que retorna 'igual' (se a negociação estiver em andamento ou um empate), 'vitória' (se a negociação for uma vitória) ou 'loose' (se a negociação for uma perda)
```python
    self.api.listinfodata.current_listinfodata.win

or

    self.api.listinfodata.get_listinfodata(foo)
```

Para obter mais informações sobre o que você realmente pode obter, use esses dados JSON que foram extraídos por frxncisjoseph durante o teste.
```py
    "name":"listInfoData","msg":[{"amount":1000000,"id":2095724656,"refund":0,"currency":"USD","currency_char":"$","active_id":1,"active":"EURUSD","value":1.07736,"exp_value":1077360,"dir":"call","created":1489706346,"expired":1489706400,"type_name":"turbo","type":"front.TU","profit":100,"profit_amount":1,"win_amount":1.74,"loose_amount":0,"sum":1,"win":"equal","now":1489706346,"user_id":0,"game_state":0,"profit_income":174,"profit_return":0,"option_type_id":3,"site_id":1,"is_demo":false,"user_balance_id":0,"client_platform_id":9,"re_track":"null","params":null}]}
```

Aqui está um trecho de código para verificar se o pedido foi cumprido e como está o andamento do pedido:
```py
    api.buy(1, 816, "turbo", "call")    
    time.sleep(0.5)

    while api.buy_successful in [None, False]:
    ....#Could do another buy order, or something else!

    print api.listinfodata.current_listinfodata.win
```
