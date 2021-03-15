using System;
using System.Drawing;
using System.Windows.Forms;

namespace Fractals
{
    struct Complex
    {
        public double x;
        public double y;
    }
    public partial class Form1 : Form
    {
        private Graphics g;
        private Pen pen;
        SettingsForm f1 = new SettingsForm();

        public Form1()
        {
            InitializeComponent();
            pen = new Pen(Color.Red);
        }
        public void drawTree(int x, int y, double l, double a)
        {
            double x1, y1;
            x1 = x + l * Math.Sin(a * Math.PI * 2 / 360);
            y1 = y + l * Math.Cos(a * Math.PI * 2 / 360);
            pen.Width = (int)(l / 20);
            g.DrawLine(pen, x, pictureBox1.Height - y, (int)x1, pictureBox1.Height - (int)y1);


            int angle1 = (int)f1.branchNumericUpDown1.Value;
            int angle2 = (int)f1.branchNumericUpDown2.Value;

            if (f1.BanchCheckBox3.Checked)
            {

                if (l > 20)
                {
                    int angle3 = (int)f1.branchNumericUpDown3.Value;

                    drawTree((int)x1, (int)y1, l / 1.2, a + angle1);
                    drawTree((int)x1, (int)y1, l / 1.2, a + angle2);
                    drawTree((int)x1, (int)y1, l / 1.2, a + angle3);
                }
            }
            else
            {
                if (l > 20)
                {
                    drawTree((int)x1, (int)y1, l / 1.2, a + angle1);
                    drawTree((int)x1, (int)y1, l / 1.2, a + angle2);
                }
            }


        }

        public void drawMandelbrot()
        {
            int n;
            Complex z, c;
            z.x = 0; z.y = 0;

            double zoom = 1 / Convert.ToDouble(f1.zoomMndlbNumericUpDown.Value);
            int b = (int)f1.eterationMndlbNumericUpDown.Value;
            for (float i = 0; i < pictureBox1.Width; i++)
            {
                for (float j = 0; j < pictureBox1.Height; j++)
                {
                    n = 0;
                    c.x = (i / pictureBox1.Width * 4 - 2) * zoom * pictureBox1.Width / pictureBox1.Height;
                    c.y = (j / pictureBox1.Height * 4 - 2) * zoom;
                    z.x = 0;
                    z.y = 0;
                    while ((z.x * z.x + z.y * z.y < 4) && (n < b))
                    {
                        double p = z.x * z.x - z.y * z.y;
                        double q = 2 * z.x * z.y;
                        z.x = c.x + p;
                        z.y = c.y + q;
                        n++;
                    }
                    if (n > b - 1)
                    {
                        g.DrawRectangle(pen, i, j, 1, 1);
                    }
                }
            }
        }
        public void drawJulia()
        {

            double iReal, iImag;

            double newReal, newImag, oldReal, oldImag;
  
            int zoom = (int)f1.zoomJuliaNumericUpDown.Value;
          
            int Iterations = (int)f1.eterationJuliaNumericUpDown.Value;

            iReal = -0.70176;
            iImag = -0.3842;

  
            for (int x = 0; x < pictureBox1.Width; x++)
                for (int y = 0; y < pictureBox1.Height; y++)
                {
                  
                    newReal = 1.5 * (x - pictureBox1.Width / 2) / (0.5 * zoom * pictureBox1.Width);
                    newImag = (y - pictureBox1.Height / 2) / (0.5 * zoom * pictureBox1.Height);

                 
                    int i;

                 
                    for (i = 0; i < Iterations; i++)
                    {

                   
                        oldReal = newReal;
                        oldImag = newImag;

                     
                        newReal = oldReal * oldReal - oldImag * oldImag + iReal;
                        newImag = 2 * oldReal * oldImag + iImag;

                   
                        if ((newReal * newReal + newImag * newImag) > 4) break;
                    }
                    int Red = (i * 9) % 255;
                    int Green = (i * 9) % 255;
                    int Blue = (i * 9) % 255;
                    //определяем цвета
                    if (f1.greenCheckBox.Checked && f1.RedCheckBox.Checked && f1.BlueCheckBox.Checked) //red+green+blue
                    {
                        pen.Color = Color.FromArgb(255, Red, Green, Blue);
                    }
                    else if (f1.RedCheckBox.Checked && f1.greenCheckBox.Checked)//red+green
                    {
                        pen.Color = Color.FromArgb(255, Red, Green, 0);
                    }
                    else if (f1.RedCheckBox.Checked && f1.BlueCheckBox.Checked)//red+blue
                    {
                        pen.Color = Color.FromArgb(255, Red, 0, Blue);
                    }
                    else if (f1.greenCheckBox.Checked && f1.BlueCheckBox.Checked)
                    {
                        pen.Color = Color.FromArgb(255, 0, Green, Blue);//green+blue
                    }
                    else if (f1.RedCheckBox.Checked)//red
                    {
                        pen.Color = Color.FromArgb(255, Red, 0, 0);
                    }
                    else if (f1.greenCheckBox.Checked)//green
                    {
                        pen.Color = Color.FromArgb(255, 0, Green, 0);
                    }
                    else if (f1.BlueCheckBox.Checked)//blue
                    {
                        pen.Color = Color.FromArgb(255, 0, 0, Blue);
                    }

                    else { MessageBox.Show("Необходимо выбрать хотя бы один цвет."); f1.ShowDialog(); }
                 
                    g.DrawRectangle(pen, x, y, 1, 1);
                }
        }



        private void Form1_Load(object sender, EventArgs e)
        {
            g = pictureBox1.CreateGraphics();
        }



        private void drawButton_Click(object sender, EventArgs e)
        {
            if (radioTreeButton.Checked)
            {
                g.Clear(Color.White);
                int length = (int)f1.lengthNumericUpDown.Value;
                drawTree(pictureBox1.Width / 2, pictureBox1.Height / 16, length, 0);
            }
            else if (radioMndlButton.Checked)
            {
                g.Clear(Color.White);
                drawMandelbrot();
            }
            else if (radioJuliaButton.Checked)
            {
                g.Clear(Color.White);
                drawJulia();
            }
            else MessageBox.Show("Выберите фрактал");

        }

        private void ClearButton_Click(object sender, EventArgs e)
        {
            g.Clear(Color.White);
        }

        private void SettingTreeButton_Click(object sender, EventArgs e)
        {
            f1.settingMandelbrotPanel.Visible = false;
            f1.settingJuliaPanel.Visible = false;
            f1.settingsTreePanel.Visible = true;
            f1.ShowDialog();
        }

        private void settingMandelbrotButton_Click(object sender, EventArgs e)
        {
            f1.settingsTreePanel.Visible = false;
            f1.settingJuliaPanel.Visible = false;
            f1.settingMandelbrotPanel.Visible = true;
            f1.ShowDialog();
        }

        private void settingsJuliaButton_Click(object sender, EventArgs e)
        {
            f1.settingMandelbrotPanel.Visible = false;
            f1.settingsTreePanel.Visible = false;
            f1.settingJuliaPanel.Visible = true;
            f1.ShowDialog();
        }
    }
}
