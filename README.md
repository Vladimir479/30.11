# 30.11
#define _CRT_SECURE_NO_DEPRECATE
#include <stdio.h>
#include <math.h>
#include <stdbool.h>
#include <stdlib.h>
#include <locale.h>
#define PI 3.14159265358979323846 




double factorial(int n);
double f(double x);
void tabulate(double start, double end, double step);
void findX(double Y, double start, double end, double step, double tolerance);
double derivative(double x, double h);
void find_exctremum(double start, double end, double step);
void log_error(const char* message);

void log_error(const char* message) {
    FILE* log_file = fopen("errors.log", "a");
    if (log_file) {
        fprintf(log_file, "ERROR: %s\n", message);
        fclose(log_file);
    }
}






int main() {
    setlocale(LC_CTYPE, "RUS");

    int choice;
    double x;
    double start;
    double end;
    double step;
    double Y, tolerance;
    double h;



    do {

        printf("\n------------Меню------------\n");
        printf("1. Значение функции в точке\n");
        printf("2. Таблица значений\n");
        printf("3.Поиск минимума/максимума \n");
        printf("4. Поиск X по Y \n");
        printf("5. Производная в точке\n");
        printf("0. Выход\n");
        printf("Выберите пункт: ");

        scanf("%d", &choice);
        switch (choice) {
        case 1:
            printf("\nВведите x: ");
            scanf("%lf", &x);
            double result = f(x);
            printf("f(%.10f) = %.10f\n", x, result);
            break;
        case 2:
            printf("\nВведите начало интервала: ");
            scanf("%lf", &start);
            printf("Введите конец интервала: ");
            scanf("%lf", &end);
            printf("Введите шаг: ");
            scanf("%lf", &step);
            if (start > end) {
                printf("Ошибка: начало интервала должно быть меньше конца!\n");
                break;
            }
            tabulate(start, end, step);
            break;
        case 3:
            printf("\nВведите начало отрезка: ");
            scanf("%lf", &start);
            printf("Введите конец отрезка: ");
            scanf("%lf", &end);
            printf("Введите шаг поиска: ");
            scanf("%lf", &step);
            find_exctremum(start, end, step);
            break;
        case 4:
            printf("\nВведите искомое значение Y: ");
            scanf("%lf", &Y);
            printf("Введите начало интервала поиска: ");
            scanf("%lf", &start);
            printf("Введите конец интервала поиска: ");
            scanf("%lf", &end);
            printf("Введите шаг поиска: ");
            scanf("%lf", &step);
            printf("Введите допустимую погрешность: ");
            scanf("%lf", &tolerance);
            findX(Y, start, end, step, tolerance);
            break;
        case 5:
            printf("\nВведите x для вычисления производной: ");
            scanf("%lf", &x);
            printf("Введите шаг h (рекомендуется 1e-6): ");
            scanf("%lf", &h);
            double deriv = derivative(x, h);
            if (deriv >= 0) {
                printf("f'(%.10f) = %.10f\n", x, deriv);
            }
            else {
                printf("Производная не может быть вычислена в точке x=%.10f\n", x);
            }
            break;



        case 0:
            printf("Завершение работы программы.\n");
            break;
        default:
            printf("Неверный выбор. Попробуйте снова.\n");
        }
    } while (choice != 0);

    return 0;
}




//Функция для вычисления факториала
double factorial(int n) {
    if (n < 0) {
        log_error("Отрицательный факториал");
        return -1.0;
    }

    double result = 1.0;
    for (int i = 2; i <= n; i++) {
        result *= i;
    }
    return result;
}

//Основная функция f(x)
double f(double x) {
    if (x < 1) {


        if (x == 2) {
            log_error("Деление на ноль при x=2");
            return -1.0;
        }
        return (x * x - 4) / (x - 2);
    }

    else if (x >= 1 && x < PI / 2) {

        if (cos(x) == 0) {
            log_error("Деление на ноль в cos(x)");
            return -1.0;
        }
        return (1.0 / cos(x)) - tan(x);
    }
    else if (x >= PI / 2) {
        double sum = 0;
        for (int n = 0; n <= 15; n++) {
            double slagaemoe = pow(-1, n) * pow(x, 3 * n) / factorial(3 * n + 1);
            sum += slagaemoe;
        }
        return sum;
    }
    return 0;
}

//Функция для табулирования
void tabulate(double start, double end, double step) {
    if (start > end) {
        log_error("Начало интервала больше конца:");
        printf("Ошибка: начало интервала не может быть больше конца\n");
        return;
    }

    printf("\n===Таблица значений===\n");
    printf("\n");
    printf("|     x  |  f(x)  |\n");
    printf("|--------|--------| \n");


    for (double x = start; x <= end; x += step) {
        double y = f(x);
        printf("|%-8.2f|%-8.2f|\n", x, y);
    }
}
//Поиск x по y
void findX(double Y, double start, double end, double step, double tolerance) {
    printf("\n=== Поиск x: f(x) = %.6f ===\n", Y);
    printf("Допустимая погрешность: %.10f\n", tolerance);
    bool found = false;
    for (double x = start; x <= end; x += step) {
        double y = f(x);
        if (y >= 0 && fabs(y - Y) < tolerance) {
            printf("Найдено: x = %.10f, f(x) = %.10f\n", x, y);
            found = true;
        }
    }
    if (!found) {
        printf("Решение не найдено на отрезке [%.2f, %.2f]\n", start, end);
    }
}

//Производная
double derivative(double x, double h) {
    double f_left = f(x - h);
    double f_right = f(x - h);
    
    if (f_left < 0 || f_right < 0) {
        return -1.0;
    }
    return (f_right - f_left) / (2 * h);
}

//Функция для максимума и минимума 
void find_exctremum(double start, double end, double step) {
    double min_val = INFINITY, max_val = -INFINITY;
    double min_x = 0, max_x = 0;
    bool found = false;

    for (double x = start; x <= end; x += step) {
        double y = f(x);
        if (y >= 0) {
            found = true;
            if (y < min_val) {
                min_val = y;
                min_x = x;
            }
            if (y > max_val) {
                max_val = y;
                max_x = x;
            }
        }
    }


    if (found) {
        printf("\n=== Экстремумы на отрезке [%.2f, %.2f] ===\n", start, end);
        printf("Минимум: f(%.6f) = %.10f\n", min_x, min_val);
        printf("Максимум: f(%.6f) = %.10f\n", max_x, max_val);
    }
    else {
        printf("Не удалось найти экстремумы на заданном отрезке\n");
    }
}
