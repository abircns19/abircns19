#include <windows.h>
#include <iostream>
#include <conio.h>
#include <stdlib.h>
#define ANCHO 59
#define ALTO 25
using namespace std;

void gotoxy(int x, int y)
{
	HANDLE hCon;
	hCon = GetStdHandle(STD_OUTPUT_HANDLE);
	COORD dwPos;
	dwPos.X = x;
	dwPos.Y = y;
	SetConsoleCursorPosition(hCon, dwPos);
}

void recuadro()
{
	for (int i = 2; i < ANCHO; i++)
	{
		gotoxy(i * 2, 0);
		cout << char(220);
		gotoxy(i * 2, ALTO);
		cout << char(220);
	}
	
	for (int i = 0; i <= ALTO; i++)
	{
		gotoxy(0, i);
		cout << char(220);
		gotoxy(ANCHO * 2 - 1, i);
		cout << char(220);
	}
}

void dibujarEje()
{
	for (int i = 0; i <= ANCHO * 2; i++)
	{
		gotoxy(i, ALTO / 2);
		cout << char(95);
	}
	for (int i = 1; i < ALTO; i++)
	{
		gotoxy(ANCHO, i);
		cout << char(179);
	}
}

void mostrarInstrucciones()
{
	system("cls");
	cout << "Juego de la matriz matematica" << endl;
	cout << "=============================" << endl;
	cout << "Instrucciones:" << endl;
	cout << "- Mueve el objeto hacia la meta utilizando las teclas de flechas." << endl;
	cout << "- Cada vez que te muevas, apareceran numeros al azar en los cuadros alrededor del objeto." << endl;
	cout << "- Calcula el total siguiendo las reglas y responde correctamente." << endl;
	cout << "- Tienes 8 intentos para llegar a la meta." << endl;
	cout << "- Si fallas, regresarás a tu posición anterior." << endl;
	cout << "- Si no alcanzas la meta en 8 intentos, perderás la partida." << endl;
	cout << "Presiona cualquier tecla para comenzar..." << endl;
	_getch();
}

void mostrarCuadro(int cuadro[5][5], int fila, int col)
{
	system("cls");
	HANDLE hConsole = GetStdHandle(STD_OUTPUT_HANDLE);
	
	for (int i = 0; i < 5; i++)
	{
		for (int j = 0; j < 5; j++)
		{
			if (i == fila && j == col)
			{
				SetConsoleTextAttribute(hConsole, 12);
				cout << "o\t";
			}
			else if (i >= fila - 1 && i <= fila + 1 && j >= col - 1 && j <= col + 1)
			{
				SetConsoleTextAttribute(hConsole, 15);
				cout << cuadro[i][j] << "\t";
			}
			else
			{
				SetConsoleTextAttribute(hConsole, 8);
				cout << char(219) << "\t";
			}
		}
		cout << endl;
	}
	SetConsoleTextAttribute(hConsole, 15);
}

int calcularTotal(int cuadro[5][5], int fila, int col)
{
	int suma_arriba = 0;
	int suma_abajo = 0;
	int mult_derecha = 0;
	int mult_izquierda = 0;
	
	
	if (fila > 0)
	{
		for (int i = fila - 1; i >= fila - 3; i--)
		{
			if (i >= 0)
				suma_arriba += cuadro[i][col];
		}
	}
	
	
	if (col < 4)
	{
		mult_derecha = cuadro[fila][col + 1];
	}
	
	
	if (fila < 4)
	{
		for (int i = fila + 1; i <= fila + 3; i++)
		{
			if (i < 5)
				suma_abajo += cuadro[i][col];
		}
	}
	
	
	if (col > 0)
	{
		mult_izquierda = cuadro[fila][col - 1];
	}
	
	return ((suma_arriba + mult_derecha) * (suma_abajo + mult_izquierda)) - (suma_arriba + mult_derecha);
}

bool verificarDestinoAlcanzado(int fila, int col)
{
	return (fila == 0 && col == 2);
}

int main()
{
	system("cls");
	system("color 40");
	
	mostrarInstrucciones();
	dibujarEje();
	
	int cuadro[5][5] = {
	{0, 0, 0, 0, 0},
	{0, 0, 0, 0, 0},
	{0, 0, 7, 4, 1},
	{0, 2, 6, 0, 0},
	{0, 3, 8, 5, 0}
	
};
	
	int fila_actual = 2;
	int col_actual = 2;
	int intentos_restantes = 8;
	
	mostrarCuadro(cuadro, fila_actual, col_actual);
	
	while (!verificarDestinoAlcanzado(fila_actual, col_actual) && intentos_restantes > 0)
	{
		cout << "Intentos restantes: " << intentos_restantes << endl;
		cout << "Ingresa tu movimiento (w: arriba, s: abajo, a: izquierda, d: derecha): ";
		
		char movimiento = _getch();
		cout << movimiento << endl;
		
		switch (movimiento)
		{
		case 'w':
			if (fila_actual - 2 >= 0)
				fila_actual -= 2;
			break;
		case 's':
			if (fila_actual + 2 < 5)
				fila_actual += 2;
			break;
		case 'a':
			if (col_actual - 2 >= 0)
				col_actual -= 2;
			break;
		case 'd':
			if (col_actual + 2 < 5)
				col_actual += 2;
			break;
		default:
			cout << "Movimiento invalido. Intenta nuevamente." << endl;
			continue;
		}
		
		mostrarCuadro(cuadro, fila_actual, col_actual);
		
		int total = calcularTotal(cuadro, fila_actual, col_actual);
		
		cout << "Total actual: " << total << endl;
		
		if (total != 0)
		{
			intentos_restantes--;
			cout << "Respuesta incorrecta. Regresando a la posicion anterior." << endl;
			_getch();
			
			fila_actual = 2;
			col_actual = 2;
			
			mostrarCuadro(cuadro, fila_actual, col_actual);
		}
	}
	
	if (verificarDestinoAlcanzado(fila_actual, col_actual))
	{
		cout << "¡Has llegado a la meta! Felicidades." << endl;
	}
	else
	{
		cout << "Has perdido. No has alcanzado la meta en el numero de intentos permitidos." << endl;
	}
	
	return 0;
}

