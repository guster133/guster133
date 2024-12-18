using System;
using System.Data;
using System.Data.SqlClient;
using System.Windows.Forms;
using Guna.UI2.WinForms; // Dodato za Guna UI elemente

namespace IgraonicaApp
{
    public partial class IgraonicaApp : Form
    {
        // Konekcioni string za SQL Server bazu podataka
        private string connectionString = "Data Source=DESKTOP-MNJAEPJ\\MSSQLSERVER01;Initial Catalog=Igraonica;Integrated Security=True;Encrypt=False;";

        // Kontrole na formi
        private Guna2DataGridView gunaDataGridView1;
        private Guna2TextBox gunaTextBoxNaziv;
        private Guna2TextBox gunaTextBoxStatus;
        private Guna2TextBox gunaTextBoxCena;
        private Guna2Button btnDodaj;
        private Guna2Button btnAzuriraj;
        private Guna2Button btnObrisi;
        private Guna2Button btnOsvezi;

        public IgraonicaApp()
        {
            InitializeComponent();
        }

        // Inicijalizacija kontrola
        private void InitializeComponent()
        {
            this.gunaDataGridView1 = new Guna2DataGridView();
            this.gunaTextBoxNaziv = new Guna2TextBox();
            this.gunaTextBoxStatus = new Guna2TextBox();
            this.gunaTextBoxCena = new Guna2TextBox();
            this.btnDodaj = new Guna2Button();
            this.btnAzuriraj = new Guna2Button();
            this.btnObrisi = new Guna2Button();
            this.btnOsvezi = new Guna2Button();

            // Form Settings
            this.Text = "Igraonica App";
            this.Size = new System.Drawing.Size(800, 600);

            // gunaDataGridView1
            this.gunaDataGridView1.Location = new System.Drawing.Point(20, 200);
            this.gunaDataGridView1.Size = new System.Drawing.Size(740, 300);

            // gunaTextBoxNaziv
            this.gunaTextBoxNaziv.PlaceholderText = "Naziv";
            this.gunaTextBoxNaziv.Location = new System.Drawing.Point(20, 20);

            // gunaTextBoxStatus
            this.gunaTextBoxStatus.PlaceholderText = "Status";
            this.gunaTextBoxStatus.Location = new System.Drawing.Point(20, 60);

            // gunaTextBoxCena
            this.gunaTextBoxCena.PlaceholderText = "Cena po satu";
            this.gunaTextBoxCena.Location = new System.Drawing.Point(20, 100);

            // btnDodaj
            this.btnDodaj.Text = "Dodaj";
            this.btnDodaj.Location = new System.Drawing.Point(20, 150);
            this.btnDodaj.Click += new EventHandler(this.btnDodaj_Click);

            // btnAzuriraj
            this.btnAzuriraj.Text = "Ažuriraj";
            this.btnAzuriraj.Location = new System.Drawing.Point(120, 150);
            this.btnAzuriraj.Click += new EventHandler(this.btnAzuriraj_Click);

            // btnObrisi
            this.btnObrisi.Text = "Obriši";
            this.btnObrisi.Location = new System.Drawing.Point(220, 150);
            this.btnObrisi.Click += new EventHandler(this.btnObrisi_Click);

            // btnOsvezi
            this.btnOsvezi.Text = "Osveži";
            this.btnOsvezi.Location = new System.Drawing.Point(320, 150);
            this.btnOsvezi.Click += new EventHandler(this.btnOsvezi_Click);

            // Dodavanje kontrola na formu
            this.Controls.Add(this.gunaDataGridView1);
            this.Controls.Add(this.gunaTextBoxNaziv);
            this.Controls.Add(this.gunaTextBoxStatus);
            this.Controls.Add(this.gunaTextBoxCena);
            this.Controls.Add(this.btnDodaj);
            this.Controls.Add(this.btnAzuriraj);
            this.Controls.Add(this.btnObrisi);
            this.Controls.Add(this.btnOsvezi);

            // Form Load Event
            this.Load += new EventHandler(this.Form1_Load);
        }

        // Funkcija za prikaz podataka u DataGridView
        private void LoadData()
        {
            using (SqlConnection conn = new SqlConnection(connectionString))
            {
                conn.Open();
                SqlDataAdapter da = new SqlDataAdapter("SELECT * FROM Racunari", conn);
                DataTable dt = new DataTable();
                da.Fill(dt);
                gunaDataGridView1.DataSource = dt; // Koriscenje Guna DataGridView
            }
        }

        // Dodavanje novog racunara u bazu
        private void btnDodaj_Click(object sender, EventArgs e)
        {
            using (SqlConnection conn = new SqlConnection(connectionString))
            {
                conn.Open();
                SqlCommand cmd = new SqlCommand("INSERT INTO Racunari (Naziv, Status, CenaPoSatu) VALUES (@Naziv, @Status, @Cena)", conn);
                cmd.Parameters.AddWithValue("@Naziv", gunaTextBoxNaziv.Text);
                cmd.Parameters.AddWithValue("@Status", gunaTextBoxStatus.Text);
                cmd.Parameters.AddWithValue("@Cena", decimal.Parse(gunaTextBoxCena.Text));
                cmd.ExecuteNonQuery();
                MessageBox.Show("Racunar je dodat u bazu.");
                LoadData();
            }
        }

        // Ažuriranje postojećeg racunara
        private void btnAzuriraj_Click(object sender, EventArgs e)
        {
            if (gunaDataGridView1.CurrentRow != null)
            {
                int id = Convert.ToInt32(gunaDataGridView1.CurrentRow.Cells["ID"].Value);
                using (SqlConnection conn = new SqlConnection(connectionString))
                {
                    conn.Open();
                    SqlCommand cmd = new SqlCommand("UPDATE Racunari SET Naziv = @Naziv, Status = @Status, CenaPoSatu = @Cena WHERE ID = @ID", conn);
                    cmd.Parameters.AddWithValue("@ID", id);
                    cmd.Parameters.AddWithValue("@Naziv", gunaTextBoxNaziv.Text);
                    cmd.Parameters.AddWithValue("@Status", gunaTextBoxStatus.Text);
                    cmd.Parameters.AddWithValue("@Cena", decimal.Parse(gunaTextBoxCena.Text));
                    cmd.ExecuteNonQuery();
                    MessageBox.Show("Podaci su ažurirani.");
                    LoadData();
                }
            }
        }

        // Brisanje racunara iz baze
        private void btnObrisi_Click(object sender, EventArgs e)
        {
            if (gunaDataGridView1.CurrentRow != null)
            {
                int id = Convert.ToInt32(gunaDataGridView1.CurrentRow.Cells["ID"].Value);
                using (SqlConnection conn = new SqlConnection(connectionString))
                {
                    conn.Open();
                    SqlCommand cmd = new SqlCommand("DELETE FROM Racunari WHERE ID = @ID", conn);
                    cmd.Parameters.AddWithValue("@ID", id);
                    cmd.ExecuteNonQuery();
                    MessageBox.Show("Racunar je obrisan iz baze.");
                    LoadData();
                }
            }
        }

        // Učitavanje podataka pri pokretanju aplikacije
        private void Form1_Load(object sender, EventArgs e)
        {
            LoadData();
        }

        // Osvežavanje prikaza podataka
        private void btnOsvezi_Click(object sender, EventArgs e)
        {
            LoadData();
        }

        // Ulazna tačka aplikacije
        [STAThread]
        public static void Main()
        {
            Application.EnableVisualStyles();
            Application.SetCompatibleTextRenderingDefault(false);
            Application.Run(new IgraonicaApp());
        }
    }
}
