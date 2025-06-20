#include <iostream>
#include <string>
#include <fstream> // Biblioteca para manejo de archivos
#include <cctype>  // Biblioteca para validación de caracteres en strings
#include <limits>  // Biblioteca para manejo de límites en validación de entradas

/*Faltan las funciones de eliminar y modificar datos, el menu de usuario y validacion de datos nuevos
Falta añadir las partes de los compañeros*/

using namespace std;

// Estructura que representa una galaxia
struct Galaxia {
    int codigo;         // Código único identificador de la galaxia
    string nombre;      // Nombre de la galaxia
    int x, y, z;        // Coordenadas espaciales de la galaxia

    Galaxia* sig;       // Puntero a la siguiente galaxia en la lista enlazada

    // Constructor para inicializar una galaxia
    Galaxia(int cod, string nom, int X, int Y, int Z){
        codigo = cod;
        nombre = nom;
        x = X;
        y = Y;
        z = Z;
        sig = nullptr;
    }
} *StarCluster;  // Puntero global al inicio de la lista de galaxias

// Estructura que representa una ruta entre galaxias (arista en el grafo)
struct Ruta {
    int origen;         // Código de galaxia origen
    int destino;        // Código de galaxia destino
    double costo;       // Costo del viaje (puede ser tiempo o distancia)

    Ruta* sig;          // Puntero a la siguiente ruta en la lista enlazada

    // Constructor para inicializar una ruta
    Ruta(int orig, int dest, double cost){
        origen = orig;
        destino = dest;
        costo = cost;
        sig = nullptr;
    }
} *Route; // Puntero global al inicio de la lista de rutas

// Estructura que representa un viaje realizado por una nave
struct Viaje {
    int origen;       // Código galaxia origen del viaje
    int destino;      // Código galaxia destino del viaje
    int dia;          // Día del viaje
    int mes;          // Mes del viaje
    int año;          // Año del viaje
    double costo;     // Costo del viaje
    int identificador;// ID único del viaje

    Viaje* sig;       // Puntero al siguiente viaje en la lista enlazada

    // Constructor para inicializar un viaje
    Viaje(int orig, int dest, int d, int m, int a, double cost, int id){
        origen = orig;
        destino = dest;
        dia = d;
        mes = m;
        año = a;
        costo = cost;
        identificador = id;
        sig = nullptr;
    }

} *Trips; // Puntero global al inicio de la lista de viajes

// Estructura que representa una nave espacial
struct Nave {
    string nombre;           // Nombre de la nave
    int identificador;       // ID único de la nave

    struct subViajes *historial; // Lista enlazada de viajes realizados por la nave
    Nave* sig;                   // Puntero a la siguiente nave en la lista enlazada

    // Constructor para inicializar una nave
    Nave(string nomb, int id){
        nombre = nomb;
        identificador = id;
        historial = nullptr;
        sig = nullptr;
    }
} *Naves; // Puntero global al inicio de la lista de naves

// Estructura para enlazar un viaje a la lista de viajes de una nave (sublista)
struct subViajes {
    Viaje* viaje;           // Puntero al viaje asociado
    subViajes* sig;         // Puntero al siguiente subViaje en la lista

    // Constructor para inicializar un subViaje
    subViajes(Viaje* v) {
        viaje = v;
        sig = nullptr;
    }
};


/* Parte de validaciones de datos */

// Valida que una cadena solo contenga letras o espacios
bool validar(string nom){
    for (char c : nom){
        if (!isalpha(c) && !isspace(c)) { 
            return false; // Si encuentra un caracter inválido retorna falso
        }
    }
    return true; // Todos los caracteres son válidos
}

// Valida que la entrada sea un número entero válido
int validarEntero() {
    int n;
    while (true) {
        cin >> n;

        if (cin.fail()) { // Si la entrada no es un entero válido
            cin.clear(); // Limpia el estado de error
            cin.ignore(numeric_limits<streamsize>::max(), '\n'); // Ignora hasta fin de línea
            cout << "Entrada inválida. Solo se permiten números enteros." << endl;
        } else {
            cin.ignore(numeric_limits<streamsize>::max(), '\n'); // Limpia buffer
            return n; // Retorna el entero leído
        }
    }
}

// Busca una galaxia por su código y devuelve un puntero a ella
Galaxia *buscarGalaxia(int cod){
    Galaxia *buscar = StarCluster;

    if(StarCluster == nullptr){
        return nullptr; // Lista vacía
    }

    // Recorre la lista hasta encontrar el código o llegar al final
    while(buscar != nullptr && buscar->codigo != cod){
        buscar = buscar->sig;
    }

    return buscar; // Retorna puntero a galaxia encontrada o nullptr si no existe
}

// Busca una ruta con los parámetros indicados
Ruta *buscarRuta(int orig, int dest, float cost){
    Ruta *buscar = Route;

    // Recorre la lista hasta encontrar ruta que coincida en origen, destino y costo
    while(buscar != nullptr && !(buscar->costo == cost && buscar->destino == dest && buscar->origen == orig)){
        buscar = buscar->sig;
    }

    return buscar; // Retorna puntero a ruta o nullptr si no existe
}

// Busca un viaje por su identificador
Viaje *buscarViaje(int id){
    Viaje *buscar = Trips;

    while(buscar != nullptr && buscar->identificador != id){
        buscar = buscar->sig;
    }

    return buscar;
}

// Busca una nave por su identificador
Nave *buscarNave(int id){
    Nave *buscar = Naves;

    while(buscar != nullptr && buscar->identificador != id){
        buscar = buscar->sig;
    }

    return buscar;
}


/* Inserción de datos */

// Inserta una nueva galaxia si no existe ya
void insertarGalaxia(int codi, string nombre, int x, int y, int z){
    if(buscarGalaxia(codi) != nullptr){
        cout << "La galaxia ya se encuentra ingresada." << endl;
        return;
    }

    Galaxia *nueva = new Galaxia(codi, nombre, x, y, z);
    nueva->sig = StarCluster; // Inserta al inicio de la lista
    StarCluster = nueva;
    cout << "Nueva galaxia añadida con éxito." << endl;
}

// Inserta una nueva ruta si no existe ya
void insertarRuta(int orig, int desti, float costo){
    if(buscarRuta(orig, desti, costo) != nullptr){
        cout << "La ruta ya ha sido ingresada" << endl;
        return;
    }

    Ruta *nueva = new Ruta(orig, desti, costo);
    nueva->sig = Route; // Inserta al inicio de la lista
    Route = nueva;
    cout << "La ruta ha sido agregada con éxito." << endl;
}

// Inserta un nuevo viaje si no existe ya
void insertarViaje(int orig, int destin, int dia, int mes, int año, double costo, int ide){
    if(buscarViaje(ide) != nullptr){
        cout << "El viaje ya ha sido ingresado." << endl;
        return;
    }

    Viaje *nuevo = new Viaje(orig, destin, dia, mes, año, costo, ide);
    nuevo->sig = Trips; // Inserta al inicio de la lista
    Trips = nuevo;
    cout << "Viaje ingresado con éxito." << endl;
}

// Inserta una nueva nave si no existe ya
void insertarNave(string nombre, int id){
    if(buscarNave(id) != nullptr){
        cout << "La nave ya ha sido ingresada al sistema." << endl;
        return;
    }

    Nave *nuevo = new Nave(nombre, id);
    nuevo->sig = Naves; // Inserta al inicio de la lista
    Naves = nuevo;
    cout << "Nave ingresada con éxito." << endl;
}

// Enlaza un viaje existente al historial de una nave
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
    nuevoSub->sig = navecita->historial; // Inserta al inicio de la sublista
    navecita->historial = nuevoSub;

    cout << "Viaje enlazado correctamente a la nave." << endl;
}


/* Funciones para cargar datos desde archivos */

// Carga galaxias desde archivo de texto
void cargarGalaxiasDesdeArchivo(const string& nombreArchivo) {
    ifstream archivo(nombreArchivo);
    if (!archivo) {
        cerr << "No se pudo abrir el archivo de galaxias.\n";
        return;
    }

    int codigo, x, y, z;
    string nombre;
    
    while (archivo >> codigo >> nombre >> x >> y >> z) {
        insertarGalaxia(codigo, nombre, x, y, z);
    }

    archivo.close();
}

// Carga rutas desde archivo de texto
void cargarRutasDesdeArchivo(const string& nombreArchivo) {
    ifstream archivo(nombreArchivo);
    if (!archivo) {
        cerr << "No se pudo abrir el archivo de rutas.\n";
        return;
    }

    int origen, destino;
    double costo;

    while (archivo >> origen >> destino >> costo) {
        insertarRuta(origen, destino, costo);
    }

    archivo.close();
}

// Carga naves desde archivo de texto
void cargarNavesDesdeArchivo(const string& nombreArchivo) {
    ifstream archivo(nombreArchivo);
    if (!archivo.is_open()) {
        cerr << "Error: no se pudo abrir el archivo de naves." << endl;
        return;
    }

    int id;
    string nombre;

    while (archivo >> id >> nombre) {
        insertarNave(nombre, id);
    }

    archivo.close();
}

// Carga viajes desde archivo de texto
void cargarViajesDesdeArchivo(const string& nombreArchivo) {
    ifstream archivo(nombreArchivo);
    if (!archivo.is_open()) {
        cerr << "Error: no se pudo abrir el archivo de viajes." << endl;
        return;
    }

    int origen, destino, dia, mes, anio, id;
    double costo;

    while (archivo >> origen >> destino >> dia >> mes >> anio >> costo >> id) {
        insertarViaje(origen, destino, dia, mes, anio, costo, id);
    }

    archivo.close();
}

// Función que llama a todas las funciones de carga para traer todos los datos
void cargarDatos(){
    cargarGalaxiasDesdeArchivo("galaxias.txt");
    cargarNavesDesdeArchivo("naves.txt");
    cargarRutasDesdeArchivo("rutas.txt");
    cargarViajesDesdeArchivo("viajes.txt");
}


/*Funciones de Kruskal*/




/*Funciones de consulta*/

//Mostrar por las rutas desde una galaxia
void mostrarRutasGalaxia(int idGal){
    Ruta *mostrar = Route;
    Galaxia *buscar = buscarGalaxia(idGal);

    if (buscar == nullptr) {
        cout << "La galaxia no existe.\n";
        return;
    }

    cout << "Mostrando rutas desde la galaxia " << buscar->nombre << ": " << endl;
    bool encontrado = false;
    while(mostrar != nullptr){
        if(mostrar->origen == idGal){
            cout << "Hacia galaxia " << mostrar->destino  << " | Costo: " << mostrar->costo << endl;
            encontrado = true;
        }
        mostrar = mostrar->sig;
    }

    if (!encontrado) {
        cout << "No hay rutas desde esta galaxia" << endl;;
    }
}

// Muestra la ruta de menor costo entre dos galaxias
void rutaDeMenorCosto(int idOrig, int idDest){
    Ruta *buscar = Route;
    Ruta *menor = nullptr;
    double menorCosto = numeric_limits<double>::max(); // Número muy grande

    while (buscar != nullptr) {
        if (buscar->origen == idOrig && buscar->destino == idDest && buscar->costo < menorCosto) {
            menor = buscar;
            menorCosto = buscar->costo;
        }
        buscar = buscar->sig;
    }

    if (menor == nullptr) {
        cout << "No hay ruta de la galaxia " << idOrig << " a la galaxia " << idDest << "." << endl;
    } else {
        cout << "Ruta de menor costo de " << idOrig << " a " << idDest << " tiene un costo de: " << menor->costo << endl;
    }
}


//Mostrar árbol de expansión
void imprimirArbolExpansion(const vector<Ruta*>& arbol) {
    cout << "Árbol de expansión resultante (Kruskal modificado):\n";
    for(const Ruta* r : arbol){
        cout << "Origen: " << r->origen << " -> Destino: " << r->destino << " | Costo: " << r->costo << "\n";
    }
}

// Muestra el historial de viajes de una nave
void mostrarHistorialViajesNave(int idNave) {
    Nave *nave = buscarNave(idNave);

    if (nave == nullptr) {
        cout << "La nave no se encuentra registrada en el sistema." << endl;
        return;
    }

    subViajes *mostrar = nave->historial;
    if (mostrar == nullptr) {
        cout << "La nave " << nave->nombre << " no tiene viajes registrados." << endl;
        return;
    }

    cout << "Historial de viajes de la nave " << nave->nombre << " (ID: " << nave->identificador << "):\n";

    int i = 1;
    while (mostrar != nullptr) {
        Viaje *v = mostrar->viaje;
        cout << i++ << ": " << "Origen: " << v->origen << endl << "Destino: " << v->destino << endl <<"Fecha: " << v->dia << "/" << v->mes << "/" 
        << v->año << endl << "Costo: " << v->costo << endl << "ID: " << v->identificador << endl;

        mostrar = mostrar->sig;
    }
}

// Muestra el historial de viajes de todas las naves
void historialViajesTodasNaves() {
    Nave *nave = Naves;

    if (nave == nullptr) {
        cout << "No se encuentran naves registradas en el sistema." << endl;
        return;
    }

    while (nave != nullptr) {
        subViajes *mostrar = nave->historial;

        cout << "Historial de viajes de la nave " << nave->nombre << " (ID: " << nave->identificador << "):" << endl;

        if (mostrar == nullptr) {
            cout << "  Sin viajes registrados." << endl << endl;
            nave = nave->sig;
        }

        int i = 1;
        while (mostrar != nullptr) {
            Viaje *v = mostrar->viaje;
            cout << "  " << i++ << ". Origen: " << v->origen  << endl << "Destino: " << v->destino << endl << "Fecha: " << v->dia << "/" 
            << v->mes << "/" << v->año << endl << "Costo: " << v->costo << endl << "ID de viaje: " << v->identificador << endl;
            mostrar = mostrar->sig;
        }

        cout << endl << "---------------------------" << endl; 
        nave = nave->sig;
    }
}

/*Funciones de reportes*/


/*Funciones de menu*/

int main(){
    cargarDatos();
    vector<Ruta*> arbol = kruskalModificado(Route);
    imprimirArbolExpansion(arbol);
    mostrarRutasGalaxia(101);
    rutaDeMenorCosto(101, 106);

    return 0;
}
