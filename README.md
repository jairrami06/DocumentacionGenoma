# GENOMA - Especificación del Lenguaje

## 1. Introducción

GENOMA es un pseudolenguaje declarativo diseñado para prototipar y generar aplicaciones web. Su estructura se divide en tres capas: **Modelo de Datos**, **Componentes de Interfaz** y **Estructura de Aplicación**.

---

## 2. Referencia del Lenguaje

### A. Definición de Datos (`ENTITY`)

Define la estructura de un objeto y las relaciones.

**Sintaxis:**
`ENTITY [NombreEntidad]: [atributo]:[TIPO], [RelacionEntidad]`

| Tipo de Dato | Descripción |
| --- | --- |
| `TEXT` | Cadena corta de texto |
| `TEXT_LONG` | Texto extenso | 
| `NUMBER` | Valor numérico (entero/decimal) |
| `DATE` | Fecha | Date picker |
| `BOOLEAN` | Verdadero/Falso |
| `IMAGE` | URL o archivo de imagen |
| `[Entidad]` | Nombre de otra entidad definida |

### B. Componentes de Interfaz

Elementos reutilizables que interactúan con las entidades.

#### Tabla (`TABLE`)

Muestra listados de datos con opciones de filtrado.

* **`SOURCE`**: Entidad de donde provienen los datos.
* **`DISPLAY`**: Lista de atributos a mostrar como columnas.
* **`FILTERS`**: Atributos que servirán como filtros de búsqueda.
* **`ACTIONS`**: Acciones disponibles por fila (`EDIT`, `DELETE`, `DETAILS`). Si se usa `EDIT` o `CREATE`, se debe especificar el Formulario a usar.

#### Formulario (`FORM`)

Interfaz para crear o modificar registros.

* **`SOURCE`**: Entidad a la que afecta.
* **`FIELDS`**: Campos visibles (o `ALL` para todos).
* **`VALIDATION`**: Reglas de validación (ej. `REQUIRED`).

### C. Vistas y Navegación (`VIEW`, `MENU`, `APP`)

Estructura global de la aplicación.

* **`VIEW`**: Una pantalla individual.
* `HEADER`: Título de la pantalla.
* `USE_COMPONENT([NombreComponente])`: Inserta una Tabla o Formulario.


* **`MENU`**: Define la barra de navegación lateral o superior.
* **`APP`**: Configuración global del proyecto.

---

## 3. Ejemplo Completo: Sistema de Gestión Académica

A continuación, un ejemplo funcional que integra todas las características para un sistema de gestión de cursos.

```yaml
# ==========================================
# 1. MODELADO DE DATOS (ENTITIES)
# ==========================================

ENTITY Termino: 
    nombre: TEXT, 
    fecha_inicio: DATE, 
    activo: BOOLEAN

ENTITY Institucion: 
    nombre: TEXT, 
    logo: IMAGE, 
    direccion: TEXT_LONG

ENTITY Curso: 
    nombre: TEXT, 
    codigo: TEXT, 
    cupos: NUMBER, 
    descripcion: TEXT_LONG,
    Termino,      # Relación: Un curso pertenece a un Término
    Institucion   # Relación: Un curso pertenece a una Institución

# ==========================================
# 2. COMPONENTES DE INTERFAZ (WIDGETS)
# ==========================================

# Formulario para crear/editar cursos
FORM FormularioCurso:
    SOURCE: Curso
    FIELDS: nombre, codigo, cupos, descripcion, Termino, Institucion
    VALIDATION: nombre(REQUIRED), codigo(REQUIRED), cupos(MIN:1)

# Tabla para listar cursos
TABLE TablaCursos: 
    SOURCE: Curso 
    FILTERS: Termino, Institucion, codigo
    DISPLAY: codigo, nombre, cupos, Institucion
    ACTIONS: CREATE(FormularioCurso), EDIT(FormularioCurso), DELETE

# Tabla simple para ver Instituciones
TABLE TablaInstituciones:
    SOURCE: Institucion
    DISPLAY: logo, nombre, direccion
    ACTIONS: CREATE, DELETE

# ==========================================
# 3. VISTAS (PÁGINAS)
# ==========================================

VIEW VistaDashboard:
    HEADER: "Panel Principal"
    CONTENT: "Bienvenido al sistema de gestión."

VIEW VistaGestionCursos: 
    HEADER: "Administración de Cursos"
    USE_COMPONENT(TablaCursos)

VIEW VistaInstituciones:
    HEADER: "Catálogo de Instituciones"
    USE_COMPONENT(TablaInstituciones)

# ==========================================
# 4. CONFIGURACIÓN GLOBAL Y NAVEGACIÓN
# ==========================================

MENU MenuPrincipal:
    ITEM: "Inicio", LINK: VistaDashboard
    GROUP: "Académico", ITEMS: [
        LABEL: "Cursos", LINK: VistaGestionCursos,
        LABEL: "Instituciones", LINK: VistaInstituciones
    ]

APP SistemaAcademico:
    THEME: "Light"
    MENU: MenuPrincipal

```
