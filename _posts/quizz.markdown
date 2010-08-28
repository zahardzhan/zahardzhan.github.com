# Ex 1

Draw four "cards", showing "A", "B", "2" and "3" respectively. Say
that for each card there is a letter on one side and a number on the
other side. I have a theory that if there is a vowel on one side of a
card, then there will be an even number on the other side.  Which
cards do we need to turn over and examine the other side of to
adequately test my theory?

В данном конкретном случае для проверки теории нужно перевернуть карту
"A".

Перевернув каждую из карт мы можем проверить 8 высказываний:

    A:  Г - Ч <= наша теория
        Г - Н
    B:  С - Ч
        С - Н
    2:  Ч - Г
        Ч - С
    3:  Н - Г
        Н - С

Высказыванию "каждой Гласной соответствует Четное число", "Г - Ч",
соответствует только первая карта — её и проверяем.

# Ex 2

What is a hash table, and when would you use one? Why might you prefer
using a hash to using an unsorted array, or a sorted array? What
tradeoffs are you making when you choose between a hash and a sorted
array.

Мой основной язык — Clojure, хэш в ней — основная структура данных,
поэтому используется практически везде: как замена "объектов"-хранилищ
данных в Java, как таблица диспетчеризации, как хранилище
метаинформации объектов, для возвращения из функции нескольких
именованных значений, для деструктуризации переданных в функцию
ключей-аргументов, для передачи в функцию ключей-аргументов, и еще
тысяча применений.

Массивы я практически нигде никогда не использовал — вектора их
прекрасно заменяли (и мутабельные списки в Python'е).

Относительно сортированных структур данных ничего не скажу — на
практике не доводилось их применять.

# Ex 3

Есть класс CodeGenerator, который умеет генерить код на разных языках.

    class CodeGenerator
    {
    public:
          enum Lang {JAVA, C_PLUS_PLUS, PHP};
          CodeGenerator(Lang language) { _language=language; }
          std::string generateCode()
          {
               switch(_language) {
               case JAVA:            //return generated java code
               case C_PLUS_PLUS: //return generated C++ code
               case PHP:             //return generated PHP code
               }
               throw new std::logic_error("Bad language");
          }
          std::string someCodeRelatedThing() // used in generateCode()
          {
               switch(_language) {
               case JAVA:            //return generated java-related stuff
               case C_PLUS_PLUS: //return generated C++-related stuff
               case PHP:             //return generated PHP-related stuff
               }
               throw new std::logic_error("Bad language");
          }
    private:
          Lang _language;
    }

Исходя из предположения, что количество языков будет добавляться, предложите
refactoring кода. Аргументируйте преимущество вашего кода над
существующим.

Я не писал на С++, и не большой любитель ООП-паттернов проектирования,
но этот код идеально ложиться на механизм мультиметодов (например, из
Common Lisp Object System или более общих из Clojure) с
диспетчеризацией по "языку программирования". В Java — паттерн Visitor
(честно сказать — ни разу не использовал).

# Ex 4

Write a program that prints the numbers from 1 to 100. But for multiples of three print
"Fizz" instead of the number and for the multiples of five print "Buzz". For numbers
which are multiples of both three and five print "FizzBuzz".

    def print_fizz_buzz():
        for i in range(1, 101):
            divides_5 = i % 5 == 0
            divides_3 = i % 3 == 0
            if divides_5 and divides_3:
                print "FizzBuzz"
            elif divides_3:
                print "Fizz"
            elif divides_5:
                print "Buzz"
            else:
                print i

# Ex 5

Перечислите все недостатки в коде:

    /**
      * Имплементации этого интерфейса предоставляют сервис
      * по авторизации пользователей
      */
    interface Authenticator {
       User authenticate(String login, String password) throws SqlException;
    }

    class SqlAuthenticatorImpl implements Authenticator {
       User authenticate(String login, String password) throws SqlException {
         // реализация
       }
    }

    class ServiceImpl {
       private SqlAuthenticatorImpl authenticator;
       public void setAuthenticator(SqlAuthenticatorImpl authenticator) {
          this.authenticator = authenticator;
       }
       public void doWork() {
          String login = ...;
          String password = ...;
          try {
             User user = authenticator.authenticate(login, password)
          } catch ( SqlException e ) {}
       }
    }

Не могу сказать ничего плохого.

# Ex 6

Какой недочет вы видите в следующем коде

    public function countBulletins($matchers, $groupByDirectory = false) {
      if ( $groupByDirectory ) {
        // клиент попросил посчитать объявления с группировкой по разделу
        $result = array();
        foreach ( $this->bulletins as $bulletin ) {
           if ( $matchers->isMatch($bulletin) ) {
             $result[$bulletin->getDirectoryId()]++;
           }
        }
      }else{
        // клиент попросил посчитать объявления без группировки
        $result = 0;
        foreach ( $this->bulletins as $bulletin ) {
           if ( $matchers->isMatch($bulletin) ) {
             $result++;
           }
        }
      }
      return $result;
    }

Я полагаю — дублирование циклов.

    public function countBulletins($matchers, $groupByDirectory = false) {
      if ( $groupByDirectory ) {
        $result = array();
      } else {
        $result = 0;
      }

      foreach ( $this->bulletins as $bulletin ) {
           if ( $matchers->isMatch($bulletin) ) {
              if ( $groupByDirectory ) {
                 $result[$bulletin->getDirectoryId()]++;
              } else {
                 $result++;
              }
           }
      }
      return $result;
    }

# Ex 7

Перечислите все известные вам структуры данных которые позволяют найти в них
значение за O (log n).

Если мне нужны гарантии производительности — я смотрю
http://goo.gl/fdGP — этого мне достаточно в 99.9% случаев.

# Ex 8

Напишите функцию, которая проверяет, является ли переданное слово
зеркальным (например: "шабаш", "кабак", "ротор"). Использовать стандартную
библиотеку не разрешается.

В идеале, я бы написал на Clojure:

(defn mirror-word? [word]
  (= (seq word) (reverse word)))

На Python:

def is_mirror_word(word):
    return [i for i in word] == [i for i in reversed(word)]

# Ex 9

Что напечатает следующий код? Объясните почему.

    static void Main(string[] args)
    {
       int h = 0;
       for (int i = 0; i < 10; i++)
       {
           h = h++;
       }
       Console.WriteLine(h);
    }

Запустил этот код — напечатал 0. 

В строке 
    
    h = h++;

h присваивает себе свое изначальное значение, т.е. 0, после чего
срабатывает оператор пост-инкремента ++. Проще выпороть того кто так
пишет, чем битый час мучать гугл, пытаясь нарыть в глубинах
документации как же эта хреновина вычисляется на самом деле.

# Ex 10

    public static int equi(int[] a) {
        switch (a.length) {
        case 0: return -1;
        case 1: return 0;
        }

        int lower = 0;
        int higher = 0;
        for (int i = 1; i < a.length; i++) {
            higher += a[i];
        }

        int k = 0;
        do {
            if (lower == higher) {
                return k;
            } else if (k == a.length - 1) {
                return -1;
            } else {
                lower += a[k];
                higher -= a[k + 1];
                k++;
            }
        } while (true);
    }
