using Databinding.Models;
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;
using static System.Windows.Forms.VisualStyles.VisualStyleElement;

namespace Databinding
{
    public partial class Form1 : Form
    {
        private SchoolContext context = new SchoolContext();
        private BindingSource bindingSource = new BindingSource();
        private int currentIndex = 0;
        public Form1()
        {
            InitializeComponent();
        }
        
        private void Form1_Load(object sender, EventArgs e)
        {
            bindingSource.DataSource = context.Students.ToList();
            dataGridView1.DataSource = bindingSource;

        }

        private void textBox1_TextChanged(object sender, EventArgs e)
        {

        }

       // private void Form1_Load(object sender, EventArgs e)
       // {

       // }

        private void textBox2_TextChanged(object sender, EventArgs e)
        {

        }

        private void btthem_Click(object sender, EventArgs e)
        {
            try
            {
                var student = new Student
                {
                    FullName = textBox1.Text,
                    Age = int.Parse(textBox2.Text),
                    Major = comboBox1.Text
                };

                // Kiểm tra nếu có dữ liệu trùng lặp
                var isDuplicate = context.Students.Any(s => s.FullName == student.FullName || s.Age == student.Age);
                if (isDuplicate)
                {
                    MessageBox.Show("Trùng tên hoặc mã sinh viên!", "Thông báo", MessageBoxButtons.OK, MessageBoxIcon.Warning);
                    return;
                }

                context.Students.Add(student);
                context.SaveChanges();
                MessageBox.Show("Thêm sinh viên thành công!", "Thông báo", MessageBoxButtons.OK, MessageBoxIcon.Information);

                bindingSource.DataSource = context.Students.ToList();
            }
            catch (Exception ex)
            {
                MessageBox.Show("Có lỗi khi thêm sinh viên: " + ex.Message, "Lỗi", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }
        }

        private void btsua_Click(object sender, EventArgs e)
        {
            try
            {
                if (bindingSource.Current is Student student)
                {
                    student.FullName = textBox1.Text;
                    student.Age = int.Parse(textBox2.Text);
                    student.Major = comboBox1.Text;

                    context.SaveChanges();
                    MessageBox.Show("Sửa thông tin sinh viên thành công!", "Thông báo", MessageBoxButtons.OK, MessageBoxIcon.Information);

                    bindingSource.DataSource = context.Students.ToList();
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show("Có lỗi khi sửa thông tin sinh viên: " + ex.Message, "Lỗi", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }
        }

        private void btxoa_Click(object sender, EventArgs e)
        {

            try
            {
                if (bindingSource.Current is Student student)
                {
                    var confirmResult = MessageBox.Show("Bạn có chắc chắn muốn xóa sinh viên này?", "Xác nhận xóa", MessageBoxButtons.YesNo, MessageBoxIcon.Question);
                    if (confirmResult == DialogResult.Yes)
                    {
                        context.Students.Remove(student);
                        context.SaveChanges();
                        MessageBox.Show("Xóa sinh viên thành công!", "Thông báo", MessageBoxButtons.OK, MessageBoxIcon.Information);

                        bindingSource.DataSource = context.Students.ToList();
                    }
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show("Có lỗi khi xóa sinh viên: " + ex.Message, "Lỗi", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }

        }

        private void button1_Click(object sender, EventArgs e)
        {
            if (currentIndex < bindingSource.Count - 1)
            {
                currentIndex++;
                bindingSource.Position = currentIndex;
            }
        }

        private void button2_Click(object sender, EventArgs e)
        {
            if (currentIndex > 0)
            {
                currentIndex--;
                bindingSource.Position = currentIndex;
            }
        }

        private void dataGridView1_CellContentClick(object sender, DataGridViewCellEventArgs e)
        {
            
        }

        private void dataGridView1_SelectionChanged(object sender, EventArgs e)
        {
            if (bindingSource.Current is Student student)
            {
                textBox1.Text = student.FullName;
                textBox2.Text = student.Age.ToString();
                comboBox1.Text = student.Major;
            }
        }
    }
}
