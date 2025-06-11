#include <iostream>
#include <string>
#include <cctype> //Biblioteca para validar entradas de string;
#include <limits> // Biblioteca para validar entradas de int;

using namespace std;

// Estructura para galaxias
struct Galaxia {
    int codigo;         // Código único de galaxia
    string nombre;      // Nombre de la galaxia
    int x, y, z;        // Coordenadas espaciales

    Galaxia* sig; // Apunta a la siguiente galaxia en la lista

    Galaxia(int cod, string nom, int X, int Y, int Z){ //Constructor
        codigo = cod;
        nombre = nom;
        x = X;
        y = Y;
        z = Z;
        sig = nullptr;
    }
} *StarCluster;  // Puntero global al inicio de la lista de galaxias

// Estructura para rutas entre galaxias (aristas del grafo)
struct Ruta {
    int origen;         // La galaxia origen
    int destino;        // La galaxia destino
    double costo;        // Costo del viaje (puede ser tiempo o distancia)

    Ruta* sig;    // Apunta a la siguiente ruta en la lista

    Ruta(int orig, int dest, double cost){ //Constructor
        origen = orig;
        destino = dest;
        costo = cost;
        sig = nullptr;
        
    }
} *Route;

// Estructura para viajes realizados por una nave
struct Viaje {
    int origen;       // La galaxia de origen
    int destino;      // La galaxia de destino
    int dia;           // día del viaje
    int mes;           //mes del viaje
    int año;           //Año del viaje
    double costo;       // Costo del viaje
    int identificador;

    Viaje* sig;       // Apunta al siguiente viaje en el historial

    Viaje(int orig, int dest, int d, int m, int a, double cost, int id){ //Constructor
        origen = orig;
        destino = dest;
        dia = d;
        mes = m;
        año = a;
        costo = cost;
        identificador = id;
        sig = nullptr;
    }

} *Trips;


// Estructura para naves espaciales
struct Nave {
    string nombre;          // Nombre de la nave
    int identificador;   // ID o código único de la nave

    struct subViajes *historial;       // Sublista de viajes realizados
    Nave* sig;        // Apunta a la siguiente nave en la lista

    Nave(string nomb, int id){
        nombre = nomb;
        identificador = id;
        historial = nullptr;
        sig = nullptr;

    }
} *Naves;

//Estructura de enlace entre una nave y su historial de viajes.
struct subViajes {
    Viaje* viaje;           // Puntero al viaje
    subViajes* sig;         // Apunta al siguiente subViaje

    subViajes(Viaje* v) {
        viaje = v;
        sig = nullptr;
    }
};


/*Parte de las validaciones de datos, faltan algunas validaciones más (como verificar que el double sea correcto y positivo)*/

//Función que valida que la entrada sea valida.
bool validar(string nom){
    for (char c : nom){
        if (!isalpha(c) && !isspace(c)) { 
            return false;
        }
    }
    return true;
}

//Función que valida que la entrada sea un entero.
int validarEntero() {
    int n;
    while (true) {
        cin >> n;

        if (cin.fail()) {
            cin.clear();
            cin.ignore(numeric_limits<streamsize>::max(), '\n');
            cout << "Entrada inválida. Solo se permiten números enteros." << endl;
        } else {
            cin.ignore(numeric_limits<streamsize>::max(), '\n');
            return n;
        }
    }
}

//Funcion para buscar una galaxia y  devuelve un puntero
Galaxia *buscarGalaxia(int cod){
    Galaxia *buscar = StarCluster;

    if(StarCluster==nullptr){
        return nullptr;
    }

    while(buscar!=nullptr && buscar->codigo!=cod){
        buscar = buscar->sig;
    }

    return buscar;
}

Ruta *buscarRuta(int orig, int dest, float cost){
    Ruta *buscar = Route;

    while(buscar!=nullptr && !(buscar->costo==cost && buscar->destino==dest && buscar->origen==orig)){
        buscar = buscar ->sig;
    }

    return buscar;
}

Viaje *buscarViaje(int id){
    Viaje *buscar = Trips;

    while(buscar != nullptr && buscar->identificador!=id){
        buscar = buscar->sig;
    }

    return buscar;
}


Nave *buscarNave(int id){
    Nave *buscar = Naves;

    while(buscar != nullptr && buscar->identificador!=id){
        buscar = buscar->sig;
    }

    return buscar;
}


/*Inserción de datos*/

//Función para insertar una galaxia
void insertarGalaxia(int codi, string nombre, int x, int y, int z){
    if(buscarGalaxia(codi)!=nullptr){
        cout << "La galaxia ya se encuentra ingresada." << endl;
        return;
    }

    Galaxia *nueva = new Galaxia(codi, nombre, x, y, z);
    nueva ->sig = StarCluster;
    StarCluster = nueva;
    cout << "Nueva galaxia añadida con éxito." << endl;
}

void insertarRuta(int orig, int desti, float costo){
    if(buscarRuta(orig, desti, costo) != nullptr){
        cout << "La ruta ya ha sido ingresada" << endl;
        return;
    }

    Ruta *nueva = new Ruta(orig, desti, costo);
    nueva->sig = Route;
    Route = nueva;
    cout << "La ruta ha sido agregada con éxito." << endl;
}

void insertarViaje(int orig, int destin, int dia, int mes, int año, double costo, int ide){
    if(buscarViaje(ide)!=nullptr){
        cout << "El viaje ya ha sido ingresado." << endl;
        return;
    }

    Viaje *nuevo = new Viaje(orig, destin, dia, mes, año, costo, ide);
    nuevo ->sig = Trips;
    Trips = nuevo;
    cout << "Viaje ingresado con éxito." << endl;
}

void insertarNave(string nombre, int id){
    if(buscarNave(id)!=nullptr){
        cout << "La nave ya ha sido ingresada al sistema." << endl;
        return;
    }

    Nave *nuevo = new Nave(nombre, id);
    nuevo -> sig = Naves;
    Naves = nuevo;
    cout << "Nave ingresada con éxito." << endl;
}


void enlazarNaveViaje(int id, int idViaje){
    Nave *navecita = buscarNave(id);
    Viaje *viaje = buscarViaje(idViaje);

    if(navecita == nullptr){
        cout << "La nave no se encuentra ingresada." << endl;
        return;
    }

    if(viaje == nullptr){
        cout << "El viaje no se encuentra ingresado." << endl;
        return;
    }

    
    subViajes *nuevoSub = new subViajes(viaje);

    nuevoSub->sig = navecita->historial;
    navecita->historial = nuevoSub;

    cout << "Viaje enlazado correctamente a la nave." << endl;
}


int main(){
    return 0;
}
