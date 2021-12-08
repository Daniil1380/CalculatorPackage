# Calculator Protocol

## Format

Все коммуникации внутри протокола осуществляются в едином формате. Формат сообщения представлен ниже:

```
                                    1  1  1  1  1  1
      0  1  2  3  4  5  6  7  8  9  0  1  2  3  4  5
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |SP|OPRT |    ERROR     |          ID           |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                      TIME                     |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                   FIRST ARG                   |
    |                                               |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                   SECOND ARG                  |
    |                                               |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    
```

- **SP** - однобитовое поле, обозначающее, быстрая операция или долгая. 1 - быстрая. Ответ сервера всегда имеет поле, равное нулю.
- **OPRT** - двух-битовое поле, обозначает действие:
  - **0** - (сложение, если SP = 1) или (корень, если SP = 0).
  - **1** - (вычитание, если SP = 1) или (факториал, если SP = 0).
  - **2** - (умножение, если SP = 1) или (OPERATION_ERROR, если SP = 0).
  - **3** - (деление, если SP = 1) или (OPERATION_ERROR, если SP = 0).
- **ERROR** - пяти-битовое поле, четырёхбитное поле, отображающее ошибку:
  - **0** - нет ошибок.
  - **1** - *OPERATION_ERROR* - не существует такой комбинации SP и OPRT.
  - **2** - *ZERO_DIVISION* - деление на ноль.
  - **3** - *INVALID_ARG* - не целое число для факториала и другие зарезервированные случаи.
  - **4** - *IDENTIFIER_REPEAT* - Идентификатор уже использован в текущей операции.
  - **5** - *OUT_OF_BOUNDS* - число больше максимального размера ответа. 
  - **6** - *TIME_OUT* - ошибка времени выполнения, сервер не успел рассчитать ответ за отведенное время .
  - **7** - *GROVE_STREET_FAMILIES* - пасхальная ошибка в честь выхода переиздания GTA: SAN_ANDREAS, возможно получить если поделить 1992 на 4. При этом результат операции также приходит в ответе.
  - **8-15** - зарезервированы для будущего использования. (САНЯ, если какая-то ошибка еще нужна будет, бери отсюда)
- **ID** - восьмибитное поле, которое содержит уникальный идентификатор операции. Идентификатор операции заполняется клиентом для идентификации операции. Могут коллизировать, но только у разных клиентов, при получении двух операций с одним ID от одного клиента - *IDENTIFIER_REPEAT* ошибка.
- **TIME** - 16-ти битное поле, которое содержит количество секунд, которое готов ждать клиент до ошибки TIME_OUT.
- **FIRST ARG** - 64 битное поле, передается в double-формате. В случае если это запрос - то в этом поле содержится первый аргумент для вычисления, если же это ответ сервера, то в этом поле содержится ответ. 

- **SECOND ARG** - (опционально, если SP == 1, то поле присутствует), 64 битное поле, передается в double-формате. Поле для хранения второго аргумента в запросе. В ответе сервера всегда отсутствует. 
