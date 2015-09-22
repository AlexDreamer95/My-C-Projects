# My-C-Projects
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;
using System.Collections;
using System.IO;

namespace CsvToHtmlParserGUI
{
    public partial class Form1 : Form
    {
        List<String> myArrayList;

        public Form1()
        {
            InitializeComponent();
            myArrayList = new List<string>();
        }

        private bool CheckForEmptyCells()
        {
            bool isEmpty = false;
            for (int i = 0; i < dataGridView1.RowCount - 1; i++)
            {
                for (int j = 0; j < dataGridView1.ColumnCount; ++j)
                {
                    if (dataGridView1[j, i].Value == null || dataGridView1[j, i].Value == DBNull.Value || String.IsNullOrWhiteSpace(dataGridView1[j, i].Value.ToString()))
                    {
                        isEmpty = true;
                        MessageBox.Show("Empty cell occured!. Table cannot be saved");
                        return isEmpty;
                    }
                }
            }

            return false;
        }

        private void OpenTableButton_Click(object sender, EventArgs e)
        {
            if (openFileDialog1.ShowDialog() == DialogResult.OK)
            {
                List<String> cells = new List<String>();
                String filename = openFileDialog1.FileName;
                using (StreamReader sr = new StreamReader(filename))
                {
                    while (!sr.EndOfStream)
                    {
                        string line = sr.ReadLine();
                        String[] arrLine = line.Split(';');

                        if (arrLine[0] == "Id")
                            continue;

                        dataGridView1.Rows.Add(arrLine);
                        cells = arrLine.ToList<String>();
                        foreach(string str in cells)
                        {
                            myArrayList.Add(str);
                        }
                    }
                }

            }

        }

        private void SaveTableButton_Click(object sender, EventArgs e)
        {
            if (!CheckForEmptyCells())
            {
                if (saveFileDialog1.ShowDialog() == DialogResult.OK)
                {
                    String filename = saveFileDialog1.FileName;
                    StreamWriter file = new StreamWriter(filename.ToString(), false);

                    for (int i = 0; i < dataGridView1.ColumnCount; i++)
                    {
                        String header = dataGridView1.Columns[i].HeaderText;
                        file.Write(header);

                        if (i < 3)
                            file.Write(";");
                    }

                    file.WriteLine();

                    foreach (DataGridViewRow row in dataGridView1.Rows)
                    {
                        for (int i = 0; i < dataGridView1.ColumnCount; i++)
                        {
                            if (row.Cells[i].Value != null)
                            {
                                String header = dataGridView1.Columns[i].HeaderText;
                                String value = row.Cells[i].Value.ToString();
                                file.Write(value);

                                if (i < 3)
                                    file.Write(";");

                            }
                        }
                        file.WriteLine();
                    }

                    file.Close();
                }
            }
        }

        private void ConvertAndSaveButton_Click(object sender, EventArgs e)
        {
            if (!CheckForEmptyCells())
            {
                if (saveFileDialog1.ShowDialog() == DialogResult.OK)
                {
                    List<String> cells = new List<String>();
                    String filename = saveFileDialog1.FileName;
                    StreamWriter file = new StreamWriter(filename.ToString(), false);
                    file.WriteLine("<HEAD />");
                    file.WriteLine("<BODY>");
                    file.WriteLine("<table border=\"1\">");

                    file.Write("<tr/>");
                    for (int i = 0; i < dataGridView1.ColumnCount; i++)
                    {
                        file.Write("<td/>");
                        String header = dataGridView1.Columns[i].HeaderText;
                        file.Write(header);
                        file.Write("</td>");
                    }
                    file.Write("</tr>");

                    foreach (DataGridViewRow row in dataGridView1.Rows)
                    {
                        file.Write("<tr/>");
                        for (int i = 0; i < dataGridView1.ColumnCount; i++)
                        {
                            if (row.Cells[i].Value != null)
                            {
                                file.Write("<td/>");
                                String value = row.Cells[i].Value.ToString();
                                file.Write(value);

                                file.Write("</td>");
                            }
                        }
                        file.Write("</tr>");
                    }

                    file.Write("</table>");
                    file.Write("</BODY>");

                    MessageBox.Show("File saved successfully!");
                    file.Close();

                }
            }
        }

        private void ClearTableButton_Click(object sender, EventArgs e)
        {
            foreach (DataGridViewRow row in dataGridView1.Rows)
            {
                for (int i = 0; i < dataGridView1.ColumnCount; i++)
                {
                    row.Cells[i].Value = "";
                }
            }
        }

        private void dataGridView1_CellValidating(object sender, DataGridViewCellValidatingEventArgs e)
        {
            int currentRow = e.RowIndex;
            int currentCol = e.ColumnIndex;
            int res = 0;
            int age = 0;
            if (currentCol == 0 && !int.TryParse(e.FormattedValue.ToString(), out res))
            {
                if (e.FormattedValue.ToString() != "")
                {
                    dataGridView1.Rows[e.RowIndex].ErrorText =
            "Id must be digital";
                    e.Cancel = true;
                }
            }
            else if (e.ColumnIndex > 0 && e.ColumnIndex < 3 && !e.FormattedValue.ToString().All(char.IsLetter))
            {
               
                if (e.FormattedValue.ToString() != "")
                {
                        dataGridView1.Rows[e.RowIndex].ErrorText =
                "This Cell cannot be empty!";
                        e.Cancel = true;
                }

            }
            else if(e.ColumnIndex == 3 && (!int.TryParse(e.FormattedValue.ToString(), out age)
                || age < 15 || age > 100))
            {
                if (e.FormattedValue.ToString() != "")
                {
                    dataGridView1.Rows[e.RowIndex].ErrorText =
            "Age is not valid";
                    e.Cancel = true;
                }
            }
        }

        private void dataGridView1_CellEndEdit(object sender, DataGridViewCellEventArgs e)
        {
            dataGridView1.Rows[e.RowIndex].ErrorText = String.Empty;
        }
    }
}
