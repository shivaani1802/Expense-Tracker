# Functions
def list_all_expenses():
  global connector, table

  table.delete(*table.get_children())

  all_data = connector.execute('SELECT * FROM ExpenseTracker')
  data = all_data.fetchall()

  for values in data:
     table.insert('', END, values=values)


def view_expense_details():
  global table
  global date, payee, desc, amnt, MoP

  if not table.selection():
     mb.showerror('No expense selected', 'Please select an expense from the table to view its details')

  current_selected_expense = table.item(table.focus())
  values = current_selected_expense['values']

  expenditure_date = datetime.date(int(values[1][:4]), int(values[1][5:7]), int(values[1][8:]))

  date.set_date(expenditure_date) ; payee.set(values[2]) ; desc.set(values[3]) ; amnt.set(values[4]) ; MoP.set(values[5])


def clear_fields():
  global desc, payee, amnt, MoP, date, table

  today_date = datetime.datetime.now().date()

  desc.set('') ; payee.set('') ; amnt.set(0.0) ; MoP.set('Cash'), date.set_date(today_date)
  table.selection_remove(*table.selection())


def remove_expense():
  if not table.selection():
     mb.showerror('No record selected!', 'Please select a record to delete!')
     return

  current_selected_expense = table.item(table.focus())
  values_selected = current_selected_expense['values']

  surety = mb.askyesno('Are you sure?', f'Are you sure that you want to delete the record of {values_selected[2]}')

  if surety:
     connector.execute('DELETE FROM ExpenseTracker WHERE ID=%d' % values_selected[0])
     connector.commit()

     list_all_expenses()
     mb.showinfo('Record deleted successfully!', 'The record you wanted to delete has been deleted successfully')


def remove_all_expenses():
  surety = mb.askyesno('Are you sure?', 'Are you sure that you want to delete all the expense items from the database?', icon='warning')

  if surety:
     table.delete(*table.get_children())

     connector.execute('DELETE FROM ExpenseTracker')
     connector.commit()

     clear_fields()
     list_all_expenses()
     mb.showinfo('All Expenses deleted', 'All the expenses were successfully deleted')
  else:
     mb.showinfo('Ok then', 'The task was aborted and no expense was deleted!')


def add_another_expense():
  global date, payee, desc, amnt, MoP
  global connector

  if not date.get() or not payee.get() or not desc.get() or not amnt.get() or not MoP.get():
     mb.showerror('Fields empty!', "Please fill all the missing fields before pressing the add button!")
  else:
     connector.execute(
     'INSERT INTO ExpenseTracker (Date, Payee, Description, Amount, ModeOfPayment) VALUES (?, ?, ?, ?, ?)',
     (date.get_date(), payee.get(), desc.get(), amnt.get(), MoP.get())
     )
     connector.commit()

     clear_fields()
     list_all_expenses()
     mb.showinfo('Expense added', 'The expense whose details you just entered has been added to the database')


def edit_expense():
  global table

  def edit_existing_expense():
     global date, amnt, desc, payee, MoP
     global connector, table

     current_selected_expense = table.item(table.focus())
     contents = current_selected_expense['values']

     connector.execute('UPDATE ExpenseTracker SET Date = ?, Payee = ?, Description = ?, Amount = ?, ModeOfPayment = ? WHERE ID = ?',
                       (date.get_date(), payee.get(), desc.get(), amnt.get(), MoP.get(), contents[0]))
     connector.commit()

     clear_fields()
     list_all_expenses()

     mb.showinfo('Data edited', 'We have updated the data and stored in the database as you wanted')
     edit_btn.destroy()
     return

  if not table.selection():
     mb.showerror('No expense selected!', 'You have not selected any expense in the table for us to edit; please do that!')
     return

  view_expense_details()

  edit_btn = Button(data_entry_frame, text='Edit expense', font=btn_font, width=30,
                    bg=hlb_btn_bg, command=edit_existing_expense)
  edit_btn.place(x=10, y=395)


def selected_expense_to_words():
  global table

  if not table.selection():
     mb.showerror('No expense selected!', 'Please select an expense from the table for us to read')
     return

  current_selected_expense = table.item(table.focus())
  values = current_selected_expense['values']

  message = f'Your expense can be read like: \n"You paid {values[4]} to {values[2]} for {values[3]} on {values[1]} via {values[5]}"'

  mb.showinfo('Here\'s how to read your expense', message)


def expense_to_words_before_adding():
  global date, desc, amnt, payee, MoP

  if not date or not desc or not amnt or not payee or not MoP:
     mb.showerror('Incomplete data', 'The data is incomplete, meaning fill all the fields first!')

  message = f'Your expense can be read like: \n"You paid {amnt.get()} to {payee.get()} for {desc.get()} on {date.get_date()} via {MoP.get()}"'

  add_question = mb.askyesno('Read your record like: ', f'{message}\n\nShould I add it to the database?')

  if add_question:
     add_another_expense()
  else:
     mb.showinfo('Ok', 'Please take your time to add this record')
