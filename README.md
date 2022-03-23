using MySql.Data.MySqlClient;
using System;
using System.Collections.Generic;
using System.Data;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace id404762322
{
    internal class database
    {
        #region Переменные
        static string host = "localhost"; 
        static string user = "mysql"; 
        static string pass = "mysql"; 
        static string db = "id404762322"; 
        static int port = 3306;
        //
        public double size;
        MySqlDataAdapter dataAdapter;
        DataTable bufferTable;
        #endregion
        #region Подключение
        MySqlConnection connection = new MySqlConnection($"server={host};port={port};username={user};password={pass};charset=utf8;database={db};Persist Security Info=True;");
        public void openConnection()
        {
            if (connection.State == System.Data.ConnectionState.Closed)
                connection.Open();
        }
        public void closeConnection()
        {
            if (connection.State == System.Data.ConnectionState.Open)
                connection.Close();
        }
        public MySqlConnection getConnection()
        {
            return connection;
        }
        #endregion

        public void combobox(ComboBox c, string query, string displaymember, string valuemember)
        {
            using (MySqlDataAdapter adapter = new MySqlDataAdapter(query, connection))
            {
                DataTable datatable = new DataTable();
                adapter.Fill(datatable);
                c.DataSource = datatable;
                c.DisplayMember = displaymember;
                c.ValueMember = valuemember;
                //c.SelectedIndex = 0;
            }
        }

        public DataTable fillMovie()
        {
            bufferTable = new DataTable();
            openConnection();
            try
            {
                dataAdapter = new MySqlDataAdapter("SELECT `id_movie`,name," +
                                                          "genres.id_genre,genres.genre," +
                                                           "descriptions.id_description,descriptions.description,time,year," +
                                                          "ages.id_age,ages.age," +                                                         
                                                          "countrys.id_country,countrys.country,director,studio " +
                                                            "From `movie` " +
                                                          "Inner Join genres on genres.id_genre=movie.id_genre " +
                                                          "Inner Join ages on ages.id_age=movie.id_age " +
                                                          "Inner Join descriptions on descriptions.id_description=movie.id_description " +
                                                          "Inner Join countrys on countrys.id_country=movie.id_country", connection);

                bufferTable.Clear();
                dataAdapter.Fill(bufferTable);
                closeConnection();
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.ToString());
                closeConnection();
            }
            return bufferTable;
        }

        public DataTable fillAge()
        {
            bufferTable = new DataTable();
            openConnection();
            try
            {
                dataAdapter = new MySqlDataAdapter("SELECT `id_age`,age FROM `ages`", connection);
                bufferTable.Clear();
                dataAdapter.Fill(bufferTable);
                closeConnection();
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.ToString());
                closeConnection();
            }
            return bufferTable;
        }

        public DataTable fillCountry()
        {
            bufferTable = new DataTable();
            openConnection();
            try
            {
                dataAdapter = new MySqlDataAdapter("SELECT `id_country`,country FROM `countrys`", connection);
                bufferTable.Clear();
                dataAdapter.Fill(bufferTable);
                closeConnection();
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.ToString());
                closeConnection();
            }
            return bufferTable;
        }

        public DataTable fillGenres()
        {
            bufferTable = new DataTable();
            openConnection();
            try
            {
                dataAdapter = new MySqlDataAdapter("SELECT `id_genre`,genre FROM `genres`", connection);
                bufferTable.Clear();
                dataAdapter.Fill(bufferTable);
                closeConnection();
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.ToString());
                closeConnection();
            }
            return bufferTable;
        }

        public DataTable fillDescriptions()
        {
            bufferTable = new DataTable();
            openConnection();
            try
            {
                dataAdapter = new MySqlDataAdapter("SELECT `id_description`,description FROM `descriptions`", connection);
                bufferTable.Clear();
                dataAdapter.Fill(bufferTable);
                closeConnection();
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.ToString());
                closeConnection();
            }
            return bufferTable;
        }

        public void deleteRow(DataGridView tables, string id, string table)
        {
            openConnection();
            try
            {
                DataRow row = (tables.SelectedRows[0].DataBoundItem as DataRowView).Row;
                MySqlCommand cmd = new MySqlCommand();
                cmd.Connection = connection;

                cmd.CommandText = "DELETE FROM `" + table + "` WHERE `" + id + "` = " + row[id];

                cmd.ExecuteNonQuery();
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message, "Ошибка удаления.", MessageBoxButtons.OK);
            }
            closeConnection();
        }

        public void addMovie(int id_genre, 
                             int id_age, 
                             int id_description, 
                             int id_country, 
                             int time, 
                             string director, 
                             int year, 
                             string studio, 
                             string name)
        {
            openConnection();
            try
            {
                MySqlCommand cmd = new MySqlCommand();
                cmd.Connection = connection;
                cmd.CommandText = "INSERT INTO `movie`(`id_genre`,`id_age`,`id_description`,`id_country`,`time`,`director`,`year`,`studio`,`name`) VALUES (@жанр,@год,@опис,@страна,@время,@директор,@год2,@студия,@назв)";

                cmd.Parameters.Add("@жанр", MySqlDbType.Int32);
                cmd.Parameters["@жанр"].Value = id_genre;

                cmd.Parameters.Add("@год", MySqlDbType.Int32);
                cmd.Parameters["@год"].Value = id_age;

                cmd.Parameters.Add("@опис", MySqlDbType.Int32);
                cmd.Parameters["@опис"].Value = id_description;

                cmd.Parameters.Add("@страна", MySqlDbType.Int32);
                cmd.Parameters["@страна"].Value = id_country;

                cmd.Parameters.Add("@время", MySqlDbType.Int32);
                cmd.Parameters["@время"].Value = time;

                cmd.Parameters.Add("@директор", MySqlDbType.VarChar);
                cmd.Parameters["@директор"].Value = director;

                cmd.Parameters.Add("@год2", MySqlDbType.Int32);
                cmd.Parameters["@год2"].Value = year;

                cmd.Parameters.Add("@студия", MySqlDbType.VarChar);
                cmd.Parameters["@студия"].Value = studio;

                cmd.Parameters.Add("@назв", MySqlDbType.VarChar);
                cmd.Parameters["@назв"].Value = name;

                cmd.ExecuteNonQuery();
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message, "Ошибка добавления.", MessageBoxButtons.OK);
            }
            closeConnection();
        }

        public void addAge(string date)
        {
            openConnection();
            try
            {
                MySqlCommand cmd = new MySqlCommand();
                cmd.Connection = connection;
                cmd.CommandText = "INSERT INTO `ages`(`id_age`,`age`) VALUES (NULL,@данное)";

                cmd.Parameters.Add("@данное", MySqlDbType.VarChar);
                cmd.Parameters["@данное"].Value = date + "+";

                cmd.ExecuteNonQuery();
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message, "Ошибка добавления.", MessageBoxButtons.OK);
            }
            closeConnection();
        }

        public void addDescription(string date)
        {
            openConnection();
            try
            {
                MySqlCommand cmd = new MySqlCommand();
                cmd.Connection = connection;
                cmd.CommandText = "INSERT INTO `descriptions`(`id_description`,`description`) VALUES (NULL, @данное)";

                cmd.Parameters.Add("@данное", MySqlDbType.VarChar);
                cmd.Parameters["@данное"].Value = date;

                cmd.ExecuteNonQuery();
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message, "Ошибка добавления.", MessageBoxButtons.OK);
            }
            closeConnection();
        }

        public void addCountry(string date)
        {
            openConnection();
            try
            {
                MySqlCommand cmd = new MySqlCommand();
                cmd.Connection = connection;
                cmd.CommandText = "INSERT INTO `countrys`(`id_country`,`country`) VALUES (NULL,@данное)";

                cmd.Parameters.Add("@данное", MySqlDbType.VarChar);
                cmd.Parameters["@данное"].Value = date;

                cmd.ExecuteNonQuery();
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message, "Ошибка добавления.", MessageBoxButtons.OK);
            }
            closeConnection();
        }

        public void addGenre(string date)
        {
            openConnection();
            try
            {
                MySqlCommand cmd = new MySqlCommand();
                cmd.Connection = connection;
                cmd.CommandText = "INSERT INTO `genres`(`id_genre`,`genre`) VALUES (NULL,@данное)";

                cmd.Parameters.Add("@данное", MySqlDbType.VarChar);
                cmd.Parameters["@данное"].Value = date;

                cmd.ExecuteNonQuery();
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message, "Ошибка добавления.", MessageBoxButtons.OK);
            }
            closeConnection();
        }

        public void editMovie(DataGridView table, string id, int id_genre, int id_age, int id_description, int id_country, int time, string director, int year, string studio, string name)
        {
            openConnection();
            try
            {
                foreach (DataGridViewCell oneCell in table.SelectedCells)
                {
                    if (oneCell.Selected)
                    {
                        DataRow row = (table.SelectedRows[0].DataBoundItem as DataRowView).Row;
                        MySqlCommand cmd = new MySqlCommand();
                        cmd.Connection = connection;

                        cmd.CommandText = "UPDATE `movie` SET `id_genre`=@жанр,`id_age`=@вз,`id_description`=@опис,`id_country`=@стр," +
                            "`director`=@дир,`year`=@год,`time`=@время,`studio`=@студия,`name`=@назв WHERE `id_movie`=" + row[id];

                        cmd.Parameters.Add("@жанр", MySqlDbType.Int32);
                        cmd.Parameters["@жанр"].Value = id_genre;

                        cmd.Parameters.Add("@вз", MySqlDbType.Int32);
                        cmd.Parameters["@вз"].Value = id_age;

                        cmd.Parameters.Add("@опис", MySqlDbType.Int32);
                        cmd.Parameters["@опис"].Value = id_description;

                        cmd.Parameters.Add("@стр", MySqlDbType.Int32);
                        cmd.Parameters["@стр"].Value = id_country;

                        cmd.Parameters.Add("@дир", MySqlDbType.String);
                        cmd.Parameters["@дир"].Value = director;

                        cmd.Parameters.Add("@год", MySqlDbType.Int32);
                        cmd.Parameters["@год"].Value = year;

                        cmd.Parameters.Add("@время", MySqlDbType.Int32);
                        cmd.Parameters["@время"].Value = time;

                        cmd.Parameters.Add("@студия", MySqlDbType.String);
                        cmd.Parameters["@студия"].Value = studio;

                        cmd.Parameters.Add("@назв", MySqlDbType.String);
                        cmd.Parameters["@назв"].Value = name;

                        cmd.ExecuteNonQuery();
                    }
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message, "Ошибка редактирования.", MessageBoxButtons.OK);
            }
            closeConnection();
        }

        public void editDesc(DataGridView table, string id, string desc)
        {
            openConnection();
            try
            {
                foreach (DataGridViewCell oneCell in table.SelectedCells)
                {
                    if (oneCell.Selected)
                    {
                        DataRow row = (table.SelectedRows[0].DataBoundItem as DataRowView).Row;
                        MySqlCommand cmd = new MySqlCommand();
                        cmd.Connection = connection;

                        cmd.CommandText = "UPDATE `descriptions` SET `description`=@опис WHERE `id_description`=" + row[id];

                        cmd.Parameters.Add("@опис", MySqlDbType.String);
                        cmd.Parameters["@опис"].Value = desc;

                        cmd.ExecuteNonQuery();
                    }
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message, "Ошибка редактирования.", MessageBoxButtons.OK);
            }
            closeConnection();
        }
    }
}
