# Andreea's Cooking App ( Program.cs )

## Descriere

Acest program creează și populează o bază de date phpMyAdmin pentru aplicația "Andreea's Cooking". Baza de date include tabele pentru rețete, ingrediente și aperitive.

## Structura Codului

### Importuri și Spațiul de Nume

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using MySql.Data.MySqlClient;

Aceste linii importă bibliotecile necesare pentru funcționarea programului. Bibliotecile includ funcționalități de bază ale limbajului C# și biblioteca MySQL pentru conectarea și interacțiunea cu baza de date.

Declarația Clasei Program și Inițializarea Aperitive

namespace AndreeascookingApp
{
    internal class Program
    {
        private static Aperitive[] a = new Aperitive[]
        {
            new Aperitive(1,"Mini rulouri de sunca", "(mușchi file, șuncă presată) cu cremă de hrean cu smântână și castraveciori acri (cornichon)", 15, 1, 1 ),
            new Aperitive(2,"Ruladă de jambon cu cremă de brânză","(de vaci + burduf) cu ceapă verde și ardei colorați servită pe felie de castravete", 25, 2, 2),
            new Aperitive(3,"Mini broșete (frigărui, scobitori) cu salam","cu salam crud-uscat cu crustă de ceapă și usturoi, caș maturat sau telemea, ardei gras colorat și măsline", 35, 3, 3),
            new Aperitive(4,"Ouă umplute cu cremă de brânză","și cepșoară + chipsuri crocante de mușchiuleț", 45, 4, 4),
        };

Această secțiune definește un array de obiecte Aperitive, fiecare reprezentând un aperitiv cu detalii despre nume, descriere, preț și ID-uri pentru rețetă și ingredient.

Metoda 'Main'

 static void Main(string[] args)
        {
            CreareDB();
            Console.ReadLine();
        }

Metoda Main este punctul de intrare al programului. Aceasta apelează metoda CreareDB pentru a crea baza de date și tabelele necesare și așteaptă ca utilizatorul să apese Enter înainte de a închide aplicația.

Metoda 'CreareDB'

 static private void CreareDB()
        {
            string connectionString = "Server=localhost;Uid=root;";

            using (MySqlConnection con = new MySqlConnection(connectionString))
            {
                con.Open();
                MySqlCommand cmd = new MySqlCommand("CREATE DATABASE IF NOT EXISTS Andreeascooking", con);
                cmd.ExecuteNonQuery();

                cmd.CommandText = "USE Andreeascooking";
                cmd.ExecuteNonQuery();

                cmd.CommandText = "CREATE TABLE IF NOT EXISTS retete (retete_id int NOT NULL AUTO_INCREMENT, nume varchar(255), descriere text, PRIMARY KEY (retete_id))";
                cmd.ExecuteNonQuery();

                cmd.CommandText = "CREATE TABLE IF NOT EXISTS ingrediente(id int NOT NULL AUTO_INCREMENT, nume varchar(255), PRIMARY KEY (id))";
                cmd.ExecuteNonQuery();

                cmd.CommandText = "CREATE TABLE IF NOT EXISTS aperitive (id int NOT NULL AUTO_INCREMENT, nume varchar(255), descriere text, pret decimal, reteta_id int, ingredient_id int, PRIMARY KEY (id), FOREIGN KEY (reteta_id) REFERENCES retete(retete_id), FOREIGN KEY (ingredient_id) REFERENCES ingrediente(id))";
                cmd.ExecuteNonQuery();

                Console.WriteLine("Baza de date Andreeascooking a fost creata sau exista deja");
                Console.WriteLine("Tabelele au fost create cu succes.");

                PopuleazaTabele(con);
            }
        }

Această metodă creează baza de date Andreeascooking și tabelele necesare (retete, ingrediente, aperitive). Conexiunea la baza de date se face utilizând MySqlConnection, iar comenzi SQL sunt executate folosind MySqlCommand.

Metoda 'PopuleazaTabele'

 private static void PopuleazaTabele(MySqlConnection con)
        {
            using (MySqlTransaction tx = con.BeginTransaction())
            {
                try
                {
                    MySqlCommand com = con.CreateCommand();

                    com.CommandText = "INSERT INTO retete (nume, descriere) VALUES (@nume, @descriere)";
                    foreach (var reteta in a)
                    {
                        com.Parameters.AddWithValue("@nume", reteta.Nume);
                        com.Parameters.AddWithValue("@descriere", reteta.Descriere);
                        com.ExecuteNonQuery();
                        com.Parameters.Clear();
                    }

                    com.CommandText = "INSERT INTO ingrediente (nume) VALUES (@nume)";
                    foreach (var reteta in a)
                    {
                        com.Parameters.AddWithValue("@nume", reteta.Descriere);
                        com.ExecuteNonQuery();
                        com.Parameters.Clear();
                    }

                    com.CommandText = "INSERT INTO aperitive (nume, descriere, pret, reteta_id, ingredient_id) VALUES (@nume, @descriere, @pret, @reteta_id, @ingredient_id)";
                    foreach (var aperitiv in a)
                    {
                        com.Parameters.AddWithValue("@nume", aperitiv.Nume);
                        com.Parameters.AddWithValue("@descriere", aperitiv.Descriere);
                        com.Parameters.AddWithValue("@pret", aperitiv.Pret);
                        com.Parameters.AddWithValue("@reteta_id", aperitiv.RetetaId);
                        com.Parameters.AddWithValue("@ingredient_id", aperitiv.IngredientId);
                        com.ExecuteNonQuery();
                        com.Parameters.Clear();
                    }

                    tx.Commit();
                    Console.WriteLine("Datele au fost adăugate cu succes.");
                }
                catch (Exception ex)
                {
                    Console.WriteLine("Eroare la adăugarea datelor: " + ex.Message);
                    tx.Rollback();
                }
            }
        }
    }

Această metodă populează tabelele retete, ingrediente și aperitive cu date din array-ul a. Folosește o tranzacție pentru a asigura că toate operațiunile de inserare sunt completate cu succes sau anulate în caz de eroare.

Clasa 'Aperitive'

internal class Aperitive
    {
        public int Id { get; set; }
        public string Nume { get; set; }
        public string Descriere { get; set; }
        public decimal Pret { get; set; }
        public int RetetaId { get; set; }
        public int IngredientId { get; set; }

        public Aperitive(int id, string nume, string descriere, decimal pret, int retetaId, int ingredientId)
        {
            Id = id;
            Nume = nume;
            Descriere = descriere;
            Pret = pret;
            RetetaId = retetaId;
            IngredientId = ingredientId;
        }
    }
}

Această clasă definește structura unui obiect Aperitive cu proprietăți pentru ID, nume, descriere, preț, ID-ul rețetei și ID-ul ingredientului. Constructorul clasei inițializează aceste proprietăți.

Concluzie

Acest program creează și populează baza de date pentru aplicația "Andreea's Cooking", facilitând gestionarea rețetelor și ingredientelor. Este un exemplu de utilizare a C# și MySQL pentru gestionarea bazelor de date.
