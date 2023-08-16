# int
[ur link here](https://chat.openai.com/share/1d4a5c5c-20ff-460a-af73-d41eee4b46f2)
[link 2](using Oracle.ManagedDataAccess.Client;
using System;
using System.Data;
using System.Globalization;
using System.IO;

namespace TemperatureDataToDatabase
{
    class Program
    {
        static void Main(string[] args)
        {
            string connectionString = "User Id=sys;Password=sys123;Data Source=//localhost:1521/XE;DBA Privilege=SYSDBA;";
            string csvFilePath = "C:\\Users\\nehar\\OneDrive\\Documents\\temp.csv";

            using (OracleConnection connection = new OracleConnection(connectionString))
            {
                connection.Open();
                OracleTransaction transaction = connection.BeginTransaction();

                using (OracleCommand cmd = connection.CreateCommand())
                {
                    cmd.Transaction = transaction;
                    cmd.CommandType = CommandType.Text;

                    string[] lines = File.ReadAllLines(csvFilePath);

                    foreach (string line in lines)
                    {
                        string[] values = line.Split(',');
                        if (values.Length < 2)
                        {
                            Console.WriteLine("Invalid line format: " + line);
                            continue;
                        }

                        string dateString = values[0];
                        DateTime timestamp;

                        if (!DateTime.TryParseExact(dateString, "dd-MMM-yy HH:mm:ss", CultureInfo.InvariantCulture, DateTimeStyles.None, out timestamp))
                        {
                            Console.WriteLine("Invalid date format: " + dateString);
                            continue;
                        }

                        double temperature;
                        if (!double.TryParse(values[1], out temperature))
                        {
                            Console.WriteLine("Invalid temperature value: " + values[1]);
                            continue;
                        }

                        // Insert data into the Oracle database
                        InsertDataIntoOracle(connection, timestamp, temperature);
                    }

                    // Commit the transaction after all data has been inserted
                    transaction.Commit();
                }
            }
        }

        static void InsertDataIntoOracle(OracleConnection connection, DateTime timestamp, double temperature)
        {
            using (OracleCommand cmd = connection.CreateCommand())
            {
                cmd.CommandType = CommandType.Text;

                // Assuming table columns are named Timestamp and Temperature
                cmd.CommandText = "INSERT INTO TEMP (Timestamp, Temperature) VALUES (:timestamp, :temperature)";
                cmd.Parameters.Add(":timestamp", OracleDbType.Date).Value = timestamp;
                cmd.Parameters.Add(":temperature", OracleDbType.Double).Value = temperature;

                cmd.ExecuteNonQuery();
            }
        }
    }
})
