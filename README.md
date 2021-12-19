# spreadsheet
Rapport N4( Rania Touihame / Mohamed Amine kebdani )
 # Application Using Main Window
 Create a MainWindow based application using the designer.
 
 
  # SpreadSheet (Functionality)
# Context

In the lecture on QMainWindow, we wrote the code for the graphical and set of actions for our main SpreadSeet application. Now we will focus on writing a set of basic functionality.

For the full Fonctionality, you’ll need a deep understanding on inheritance and

 * QTableSelectedRange
 * Sorting using keys.
 * Introducing a formula for each Cell ( you must use Inheritance to specialize a QTalbeWidget).
 
 In the our last iteration of the SpreadSheet we did obtain an application with:

 * Menu Bar
 * Two tools bars
 * Status bar to print the informations
 
You should have an application that looks like that :

![starter_image](https://user-images.githubusercontent.com/86810485/146681272-8765c6a2-5409-46a0-9473-34657622a575.png)

Starter Application with actions and menus.

 Remarks
 
We did add the following modifications:

 *  The now takes two ints in order to syncrhonize with the selected item from the spreadsheet. updateStatusBar
 
               void updateStatusBar(int, int) 
      
Here is the implementation of this function:

               void SpreadSheet::updateStatusBar(int row, int col)
              {
                 QString cell{"(%0, %1)"};
                 cellLocation->setText(cell.arg(row+1).arg(col+1));

              }
              
Which simply change the cellLocation text with the current cell coordinates.

  *   We added the function to connect all the actions. Here is the content of the this function:makeConnexion()

             void SpreadSheet::makeConnexions()
             {

             // --------- Connexion for the  select all action ----/
            connect(all, &QAction::triggered,
                     spreadsheet, &QTableWidget::selectAll);

              // Connection for the  show grid
            connect(showGrid, &QAction::triggered,
                     spreadsheet, &QTableWidget::setShowGrid);

             //Connection for the exit button
             connect(exit, &QAction::triggered, this, &SpreadSheet::close);

             //connectting the chane of any element in the spreadsheet with the update status bar
             connect(spreadsheet, &QTableWidget::cellClicked, this,  &SpreadSheet::updateStatusBar);             
             }


 # Go Cell
Now we will add the function for the goCell action. For that, we need to create a Dialog for the user to select a cell.

  * Create a Form Class:
  
  * Using the designer obtain the following the form:
  
              ![godidalog_ui](https://user-images.githubusercontent.com/86810485/146681851-cc8d0c25-3611-413e-9770-2776895d89ff.png)
              
  * Add the regular expression validator for the :lineEdit

               //Validating the regular expression
               QRegExp regCell{"[A-Z][1-9][0-9]{0,2}"};

              //Validating the regular expression
              ui->lineEdit->setValidator(new QRegExpValidator(regCell));

  * Add a public Getter for the line edit Text to get the cell address:

                   QString GoCellDialog::cell() const
                    {
                       return ui->lineEdit->text();
                    }
                    
No we are setup to create the interesting connexion between the goCell action:

  * First we will create the proper slot called to respond to the action trigger.goCellSlot

               private slots:
               void goCellSlot();            // Go to a Cell slot
               
  * connect the action to its proper slot in the function: makeConnexions
  
                //Connextion between the gocell action and the gocell slot
                connect(goCell, &QAction::triggered, this, &SpreadSheet::goCellSlot);

  * Now for the fun part. We will implement the function:goCellSlot()

                  void SpreadSheet::goCellSlot()
                {
                  //Creating the dialog
                  GoCellDialog D;

                  //Executing the dialog and storing the user response
                    auto reply = D.exec();

                 //Checking if the dialog is accepted
          if(reply == GoCellDialog::Accepted)
         {

         //Getting the cell text
         auto cell = D.cell();

         //letter distance
         int row = cell[0].toLatin1() - 'A';
         cell.remove(0,1);

         //second coordinate
         int col =  cell.toInt();


         //changing the current cell
         spreadsheet->setCurrentCell(row, col-1);
          }  
        }


# spreadsheet.h

 
<pre style="color:#000000;background:#ffffff;"><span style="color:#004a43; ">#</span><span style="color:#004a43; ">ifndef</span><span style="color:#004a43; "> SPREADSHEET_H</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">define</span><span style="color:#004a43; "> SPREADSHEET_H</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">&lt;</span><span style="color:#40015a; ">QAction</span><span style="color:#800000; ">&gt;</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">&lt;</span><span style="color:#40015a; ">QMainWindow</span><span style="color:#800000; ">&gt;</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">&lt;</span><span style="color:#40015a; ">QTableWidget</span><span style="color:#800000; ">&gt;</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">&lt;</span><span style="color:#40015a; ">QMenu</span><span style="color:#800000; ">&gt;</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">&lt;</span><span style="color:#40015a; ">QToolBar</span><span style="color:#800000; ">&gt;</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">&lt;</span><span style="color:#40015a; ">QLabel</span><span style="color:#800000; ">&gt;</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">&lt;</span><span style="color:#40015a; ">QStatusBar</span><span style="color:#800000; ">&gt;</span>
<span style="color:#800000; font-weight:bold; ">class</span> SpreadSheet <span style="color:#800080; ">:</span> <span style="color:#800000; font-weight:bold; ">public</span> <span style="color:#603000; ">QMainWindow</span>
<span style="color:#800080; ">{</span>
    <span style="color:#004a43; ">Q_OBJECT</span>

<span style="color:#800000; font-weight:bold; ">public</span><span style="color:#e34adc; ">:</span>
    SpreadSheet<span style="color:#808030; ">(</span><span style="color:#603000; ">QWidget</span> <span style="color:#808030; ">*</span>parent <span style="color:#808030; ">=</span> <span style="color:#800000; font-weight:bold; ">nullptr</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#808030; ">~</span>SpreadSheet<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800000; font-weight:bold; ">protected</span><span style="color:#e34adc; ">:</span>
    <span style="color:#800000; font-weight:bold; ">void</span> setupMainWindget<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">void</span> createActions<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>  <span style="color:#696969; ">// fonction pour creer les actions dde notre application</span>
    <span style="color:#800000; font-weight:bold; ">void</span> createMenus<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>  <span style="color:#696969; ">//fonction pour creer des menus</span>
    <span style="color:#800000; font-weight:bold; ">void</span> createToolBars<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">void</span> makeConnexions<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">void</span> saveContent<span style="color:#808030; ">(</span><span style="color:#603000; ">QString</span> filename<span style="color:#808030; ">)</span> <span style="color:#800000; font-weight:bold; ">const</span><span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">void</span> loadcontent<span style="color:#808030; ">(</span><span style="color:#603000; ">QString</span> filename<span style="color:#808030; ">)</span> <span style="color:#800000; font-weight:bold; ">const</span><span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">void</span> saveAsContent<span style="color:#808030; ">(</span><span style="color:#603000; ">QString</span> filename<span style="color:#808030; ">)</span> <span style="color:#800000; font-weight:bold; ">const</span><span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">void</span> loadContCSV<span style="color:#808030; ">(</span><span style="color:#603000; ">QString</span> filename<span style="color:#808030; ">)</span> <span style="color:#800000; font-weight:bold; ">const</span><span style="color:#800080; ">;</span>
<span style="color:#800000; font-weight:bold; ">public slots</span><span style="color:#e34adc; ">:</span>
     <span style="color:#800000; font-weight:bold; ">void</span> help<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
     <span style="color:#696969; ">//void close();</span>
     <span style="color:#800000; font-weight:bold; ">void</span> updateStatusBar<span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">int</span><span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">int</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>   <span style="color:#696969; ">//respond for the call changed</span>
     <span style="color:#800000; font-weight:bold; ">void</span> goCellSlot<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
     <span style="color:#800000; font-weight:bold; ">void</span> findCellSlot<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
     <span style="color:#800000; font-weight:bold; ">void</span> loadslot<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
     <span style="color:#800000; font-weight:bold; ">void</span> saveSlot<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>         <span style="color:#696969; ">//slot for saving</span>
     <span style="color:#800000; font-weight:bold; ">void</span> saveAs<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
     <span style="color:#800000; font-weight:bold; ">void</span> newFil<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
     <span style="color:#800000; font-weight:bold; ">void</span> recentFile<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
     <span style="color:#800000; font-weight:bold; ">void</span> selrow<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
     <span style="color:#800000; font-weight:bold; ">void</span> selcol<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
     <span style="color:#800000; font-weight:bold; ">void</span> deleteItem<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

<span style="color:#800000; font-weight:bold; ">public</span><span style="color:#e34adc; ">:</span>
     <span style="color:#696969; ">//centrale widget</span>
    <span style="color:#603000; ">QTableWidget</span> <span style="color:#808030; ">*</span>spreadsheet<span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//creer une variable statique k</span>
    <span style="color:#800000; font-weight:bold; ">int</span> k<span style="color:#808030; ">=</span><span style="color:#008c00; ">0</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//definir pointeur sur chaque action</span>
    <span style="color:#603000; ">QAction</span> <span style="color:#808030; ">*</span>quit<span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QAction</span> <span style="color:#808030; ">*</span>newFile<span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QAction</span> <span style="color:#808030; ">*</span>saveFile<span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QAction</span> <span style="color:#808030; ">*</span>cut<span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QAction</span> <span style="color:#808030; ">*</span>about<span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QAction</span> <span style="color:#808030; ">*</span><span style="color:#603000; ">copy</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QAction</span> <span style="color:#808030; ">*</span>paste<span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QAction</span> <span style="color:#808030; ">*</span>open<span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QAction</span> <span style="color:#808030; ">*</span>row<span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QAction</span> <span style="color:#808030; ">*</span>column<span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QAction</span> <span style="color:#808030; ">*</span>all<span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QAction</span> <span style="color:#808030; ">*</span>showGrid<span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QAction</span> <span style="color:#808030; ">*</span>deleteAction<span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QAction</span> <span style="color:#808030; ">*</span><span style="color:#603000; ">find</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QAction</span> <span style="color:#808030; ">*</span>recent<span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QAction</span> <span style="color:#808030; ">*</span>goCell<span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QAction</span> <span style="color:#808030; ">*</span>recalculate<span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QAction</span> <span style="color:#808030; ">*</span><span style="color:#603000; ">sort</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QAction</span> <span style="color:#808030; ">*</span>saveasfile<span style="color:#800080; ">;</span>


    <span style="color:#696969; ">//definir les menus de l'application</span>
    <span style="color:#603000; ">QMenu</span> <span style="color:#808030; ">*</span>file<span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QMenu</span> <span style="color:#808030; ">*</span>helpe<span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QMenu</span> <span style="color:#808030; ">*</span><span style="color:#400000; ">select</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QMenu</span> <span style="color:#808030; ">*</span>edit<span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QMenu</span> <span style="color:#808030; ">*</span>toolsMenu<span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QMenu</span> <span style="color:#808030; ">*</span>optionsMenu<span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//ahouter l'attribut de nom fichier</span>
    <span style="color:#603000; ">QString</span> <span style="color:#808030; ">*</span> currentFile<span style="color:#800080; ">;</span>


    <span style="color:#696969; ">//widgets pour la barre d'etat</span>
    <span style="color:#603000; ">QLabel</span> <span style="color:#808030; ">*</span>cellLocation<span style="color:#800080; ">;</span>    <span style="color:#696969; ">//position de la cellule active</span>
    <span style="color:#603000; ">QLabel</span> <span style="color:#808030; ">*</span>cellFormula<span style="color:#800080; ">;</span>      <span style="color:#696969; ">//formuel de la cellule active</span>

    <span style="color:#696969; ">//creer une liste pour la sauvegarde</span>
    <span style="color:#603000; ">QList</span><span style="color:#800080; ">&lt;</span><span style="color:#603000; ">QString</span><span style="color:#800080; ">&gt;</span> <span style="color:#808030; ">*</span>List<span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span><span style="color:#800080; ">;</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">endif</span><span style="color:#004a43; "> </span><span style="color:#696969; ">// SPREADSHEET_H</span>
</pre>


# spreadsheet.cpp

<pre style="color:#000000;background:#ffffff;"><span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">"</span><span style="color:#40015a; ">spreadsheet.h</span><span style="color:#800000; ">"</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include</span><span style="color:#800000; ">&lt;</span><span style="color:#40015a; ">QMenuBar</span><span style="color:#800000; ">&gt;</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include</span><span style="color:#800000; ">&lt;</span><span style="color:#40015a; ">QMenu</span><span style="color:#800000; ">&gt;</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include</span><span style="color:#800000; ">&lt;</span><span style="color:#40015a; ">QMenuBar</span><span style="color:#800000; ">&gt;</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">&lt;</span><span style="color:#40015a; ">QMessageBox</span><span style="color:#800000; ">&gt;</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">&lt;</span><span style="color:#40015a; ">QToolBar</span><span style="color:#800000; ">&gt;</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">&lt;</span><span style="color:#40015a; ">QTableWidget</span><span style="color:#800000; ">&gt;</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">"</span><span style="color:#40015a; ">godialog.h</span><span style="color:#800000; ">"</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">&lt;</span><span style="color:#40015a; ">QPixmap</span><span style="color:#800000; ">&gt;</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">"</span><span style="color:#40015a; ">finddialog.h</span><span style="color:#800000; ">"</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">&lt;</span><span style="color:#40015a; ">QFileDialog</span><span style="color:#800000; ">&gt;</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">&lt;</span><span style="color:#40015a; ">QTextStream</span><span style="color:#800000; ">&gt;</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">"</span><span style="color:#40015a; ">recent.h</span><span style="color:#800000; ">"</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include</span><span style="color:#800000; ">"</span><span style="color:#40015a; ">QFileDialog</span><span style="color:#800000; ">"</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include</span><span style="color:#800000; ">"</span><span style="color:#40015a; ">QTextStream</span><span style="color:#800000; ">"</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include</span><span style="color:#800000; ">"</span><span style="color:#40015a; ">iostream</span><span style="color:#800000; ">"</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include</span><span style="color:#800000; ">"</span><span style="color:#40015a; ">fstream</span><span style="color:#800000; ">"</span>



SpreadSheet<span style="color:#800080; ">::</span>SpreadSheet<span style="color:#808030; ">(</span><span style="color:#603000; ">QWidget</span> <span style="color:#808030; ">*</span>parent<span style="color:#808030; ">)</span>
    <span style="color:#800080; ">:</span> <span style="color:#603000; ">QMainWindow</span><span style="color:#808030; ">(</span>parent<span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    <span style="color:#696969; ">//la list</span>
    List<span style="color:#808030; ">=</span> <span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QStringList</span><span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    spreadsheet <span style="color:#808030; ">=</span> <span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QTableWidget</span><span style="color:#800080; ">;</span>
    spreadsheet <span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span> setColumnCount<span style="color:#808030; ">(</span><span style="color:#008c00; ">20</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setRowCount<span style="color:#808030; ">(</span><span style="color:#008c00; ">20</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    setCentralWidget<span style="color:#808030; ">(</span>spreadsheet<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//create action</span>
    createActions<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//create menu</span>
    createMenus<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//creation de widget</span>
    setupMainWindget<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//creation de toolbar</span>
    createToolBars<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//making connexion</span>
    makeConnexions<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//updating about the status bar</span>


    <span style="color:#696969; ">//ceating the labels for the status bar (shold be in its proper function)</span>
    cellLocation <span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QLabel</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">1,1</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    cellFormula <span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QLabel</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    statusBar<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addPermanentWidget<span style="color:#808030; ">(</span>cellLocation<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    statusBar<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addPermanentWidget<span style="color:#808030; ">(</span>cellFormula<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    statusBar<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>showMessage<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">Opening the current file</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span> <span style="color:#008c00; ">3000</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//creating a function for saving</span>
    saveSlot<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//initier le nom du fichier</span>
    currentFile <span style="color:#808030; ">=</span> <span style="color:#800000; font-weight:bold; ">nullptr</span><span style="color:#800080; ">;</span>
    setWindowTitle<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">Buffer</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//consruire les labels verticaux</span>
    <span style="color:#603000; ">QStringList</span> labels<span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">for</span><span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">auto</span> letter <span style="color:#808030; ">=</span> <span style="color:#0000e6; ">'A'</span><span style="color:#800080; ">;</span> letter<span style="color:#808030; ">&lt;</span><span style="color:#808030; ">=</span><span style="color:#0000e6; ">'Z'</span><span style="color:#800080; ">;</span> letter<span style="color:#808030; ">+</span><span style="color:#808030; ">+</span><span style="color:#808030; ">)</span>
        labels <span style="color:#808030; ">&lt;</span><span style="color:#808030; ">&lt;</span> <span style="color:#603000; ">QString</span><span style="color:#808030; ">(</span>letter<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setVerticalHeaderLabels<span style="color:#808030; ">(</span>labels<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>

SpreadSheet<span style="color:#800080; ">::</span><span style="color:#808030; ">~</span>SpreadSheet<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    <span style="color:#696969; ">//on supprime quelque pointeurs</span>
    <span style="color:#800000; font-weight:bold; ">delete</span> spreadsheet<span style="color:#800080; ">;</span>

    <span style="color:#800000; font-weight:bold; ">delete</span> quit<span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">delete</span> newFile<span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">delete</span> saveFile<span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">delete</span> cut<span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">delete</span> about<span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">delete</span> <span style="color:#603000; ">copy</span><span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">delete</span> paste<span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">delete</span> open<span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">delete</span> all<span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">delete</span> showGrid<span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">delete</span> file<span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">delete</span> helpe<span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">delete</span> edit<span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">delete</span> saveasfile<span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>

<span style="color:#800000; font-weight:bold; ">void</span> SpreadSheet<span style="color:#800080; ">::</span>createToolBars<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
   <span style="color:#696969; ">//crer une bare d'outilss</span>
    <span style="color:#800000; font-weight:bold; ">auto</span> toolbar1 <span style="color:#808030; ">=</span>addToolBar<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">&amp;File1</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>


    <span style="color:#696969; ">//ajouter des actions a cette bar</span>
    toolbar1<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addAction<span style="color:#808030; ">(</span>newFile<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    toolbar1<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addAction<span style="color:#808030; ">(</span>saveFile<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    toolbar1<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addAction<span style="color:#808030; ">(</span>open<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    toolbar1<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addSeparator<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    toolbar1<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addAction<span style="color:#808030; ">(</span>quit<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//creer une autre tool bar</span>
    <span style="color:#696969; ">//auto toolbar2 =addToolBar("&amp;File2");</span>
    <span style="color:#696969; ">//toolbar2-&gt;addAction(find);</span>


<span style="color:#800080; ">}</span>


<span style="color:#800000; font-weight:bold; ">void</span> SpreadSheet<span style="color:#800080; ">::</span>createActions<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
    <span style="color:#696969; ">//pour creer une action il faut 4etapes</span>
    <span style="color:#696969; ">//1  lui choisire une icone</span>
    <span style="color:#603000; ">QPixmap</span> quitIcon<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">:/icons/quit_icon.png</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#696969; ">//2  lui choisire un nom</span>
    quit <span style="color:#808030; ">=</span> <span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QAction</span><span style="color:#808030; ">(</span>quitIcon<span style="color:#808030; ">,</span> <span style="color:#800000; ">"</span><span style="color:#0000e6; ">&amp;Quit</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#696969; ">//3    lui choisire un reccoursi</span>
    quit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setShortcut<span style="color:#808030; ">(</span><span style="color:#400000; ">tr</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">Ctrl+Q</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#696969; ">//4    lui choisire un slot</span>
    <span style="color:#696969; ">//connect(quit, &amp;QAction::triggered, this, &amp;QMainWindow::close);</span>

    <span style="color:#696969; ">//--&gt;  creer l'action new file</span>
    <span style="color:#603000; ">QPixmap</span> newIcon<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">:/icons/new_file.png</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    newFile<span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QAction</span><span style="color:#808030; ">(</span>newIcon<span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">&amp;NewFile</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    newFile<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setShortcut<span style="color:#808030; ">(</span><span style="color:#400000; ">tr</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">Ctrl+N</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#696969; ">//connect(newFile,&amp;QAction::triggered, this, &amp;QMainWindow::create);</span>

    <span style="color:#696969; ">//--&gt;  creer le open action</span>
    <span style="color:#603000; ">QPixmap</span> openIcon<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">:/icons/open.png</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    open<span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QAction</span><span style="color:#808030; ">(</span>openIcon<span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">&amp;Open</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span><span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    open<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setShortcut<span style="color:#808030; ">(</span><span style="color:#400000; ">tr</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">Ctrl+O</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//--&gt;  creer l'action de save</span>
    <span style="color:#603000; ">QPixmap</span> saveIcon<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">:/icons/save_file.png</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    saveFile<span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QAction</span><span style="color:#808030; ">(</span>saveIcon<span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; "> &amp;SaveFile</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    saveFile<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setShortcut<span style="color:#808030; ">(</span><span style="color:#400000; ">tr</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">Ctrl+S</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//--&gt;  creer l'action cut</span>
    <span style="color:#603000; ">QPixmap</span> cutIcon<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">:/icons/cut_file.png</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    cut<span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QAction</span><span style="color:#808030; ">(</span>cutIcon<span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; "> &amp;CutFile</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    cut<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setShortcut<span style="color:#808030; ">(</span><span style="color:#400000; ">tr</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">Ctrl+X</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//--&gt;  creer l'action copy</span>
    <span style="color:#603000; ">QPixmap</span> copyIcon<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">:/icons/copy_file.png</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">copy</span><span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QAction</span><span style="color:#808030; ">(</span>copyIcon<span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; "> &amp;CopyFile</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">copy</span><span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setShortcut<span style="color:#808030; ">(</span><span style="color:#400000; ">tr</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">Ctrl+C</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//--&gt;  creer l'action open</span>
    open <span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QAction</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">&amp;Open</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span><span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    open <span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setShortcut<span style="color:#808030; ">(</span><span style="color:#400000; ">tr</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">Ctrl+O</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//--&gt;  creer l'action de saveasfile</span>
    <span style="color:#603000; ">QPixmap</span> saveasIcon<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">:/icons/saveas.png</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    saveasfile <span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QAction</span><span style="color:#808030; ">(</span>saveasIcon<span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">&amp;SaveAsFile</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#696969; ">//saveasfile-&gt;setShortcut(tr("Ctrl+"))</span>
    <span style="color:#696969; ">//connect(newfile, &amp;QAction::triggered,this, &amp;QMainWindow::create);</span>



    <span style="color:#696969; ">//--&gt;  creer l'ction paste</span>
    <span style="color:#603000; ">QPixmap</span> pasteIcon<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">:/icons/paste_file.png</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    paste<span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QAction</span><span style="color:#808030; ">(</span>pasteIcon<span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; "> &amp;PasteFile</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    paste<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setShortcut<span style="color:#808030; ">(</span><span style="color:#400000; ">tr</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">Ctrl+V</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//--&gt;  creer l'action about</span>
    about<span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QAction</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">&amp;About Us</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#696969; ">//connect(about, &amp;QAction::triggered, this, &amp;SpreadSheet::help);</span>

    <span style="color:#696969; ">//--&gt;  creer l'ction delete</span>
    <span style="color:#603000; ">QPixmap</span> deleteIcon<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">:/icons/delete.png</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    deleteAction <span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QAction</span><span style="color:#808030; ">(</span>deleteIcon<span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">&amp;Delete</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    deleteAction<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setShortcut<span style="color:#808030; ">(</span><span style="color:#400000; ">tr</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">Del</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//--&gt;  crer le row</span>
    <span style="color:#603000; ">QPixmap</span> rowIcon<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">:/icons/row.png</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    row <span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QAction</span><span style="color:#808030; ">(</span>rowIcon<span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">&amp;Row</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//--&gt;  creer le column</span>
    <span style="color:#603000; ">QPixmap</span> colIcon<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">:/icons/col.png</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    column <span style="color:#808030; ">=</span> <span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QAction</span><span style="color:#808030; ">(</span>colIcon<span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">&amp;Column</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//--&gt;  creeer le all</span>
    <span style="color:#603000; ">QPixmap</span> selectIcon<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">:/icons/select.png</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    all <span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QAction</span><span style="color:#808030; ">(</span>selectIcon<span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">&amp;All </span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    all<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setShortcut<span style="color:#808030; ">(</span><span style="color:#400000; ">tr</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">Ctrl+A</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#696969; ">//connect(all, &amp;QAction::triggered, spreadsheet, &amp;QTableWidget::selectAll);</span>

    <span style="color:#696969; ">//--&gt;  creer l'action de recent</span>
    <span style="color:#603000; ">QPixmap</span> recentIcon<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">:/icons/recent.png</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    recent<span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QAction</span><span style="color:#808030; ">(</span>recentIcon<span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">&amp;Recent</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span><span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//--&gt;  creer l'action de find</span>
    <span style="color:#603000; ">QPixmap</span> searchIcon<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">:/icons/search_icon.png</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">find</span><span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QAction</span><span style="color:#808030; ">(</span>searchIcon<span style="color:#808030; ">,</span> <span style="color:#800000; ">"</span><span style="color:#0000e6; ">&amp;Find</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">find</span><span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setShortcut<span style="color:#808030; ">(</span><span style="color:#400000; ">tr</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">Ctrl+F</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//--&gt;  creer l'action de go to cell</span>
    <span style="color:#603000; ">QPixmap</span> gotoIcon<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">:/icons/goto.png</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    goCell<span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QAction</span><span style="color:#808030; ">(</span>gotoIcon<span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">&amp;GoCell</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    goCell<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setShortcut<span style="color:#808030; ">(</span><span style="color:#400000; ">tr</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">F5</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//--&gt;  creer l'action de recalculate</span>
    recalculate<span style="color:#808030; ">=</span> <span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QAction</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">&amp;recalculate</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    recalculate<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setShortcut<span style="color:#808030; ">(</span><span style="color:#400000; ">tr</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">F9</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//--&gt;  creer l'action de sort</span>
    <span style="color:#603000; ">QPixmap</span> sortIcon<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">:/icons/sort.png</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">sort</span> <span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QAction</span><span style="color:#808030; ">(</span>sortIcon<span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">&amp;sort</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>



    <span style="color:#696969; ">//il faut ajouter le showGrid pour que la 2eme connection fonctionne</span>
    showGrid <span style="color:#808030; ">=</span> <span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QAction</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">&amp;showgrid </span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    showGrid<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setCheckable<span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">true</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    showGrid<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setChecked<span style="color:#808030; ">(</span>spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>showGrid<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#400000; ">connect</span><span style="color:#808030; ">(</span>showGrid<span style="color:#808030; ">,</span> <span style="color:#808030; ">&amp;</span><span style="color:#603000; ">QAction</span><span style="color:#800080; ">::</span>triggered<span style="color:#808030; ">,</span> spreadsheet<span style="color:#808030; ">,</span> <span style="color:#808030; ">&amp;</span><span style="color:#603000; ">QTableWidget</span><span style="color:#800080; ">::</span>setShowGrid<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>



<span style="color:#800080; ">}</span>
<span style="color:#800000; font-weight:bold; ">void</span> SpreadSheet<span style="color:#800080; ">::</span>updateStatusBar<span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">int</span> row<span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">int</span> col<span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
<span style="color:#696969; ">//    cellLocation =new QLabel("A4");</span>
<span style="color:#696969; ">//    cellFormula = new QLabel("A1 + A2");</span>
<span style="color:#696969; ">//    statusBar()-&gt;addPermanentWidget(cellLocation);</span>
<span style="color:#696969; ">//    statusBar()-&gt;addPermanentWidget(cellFormula);</span>
<span style="color:#696969; ">//    statusBar()-&gt;showMessage("Opening the current file", 3000);</span>
    <span style="color:#603000; ">QString</span> cell<span style="color:#800080; ">{</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">%0,%1</span><span style="color:#800000; ">"</span><span style="color:#800080; ">}</span><span style="color:#800080; ">;</span>
    cellLocation<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setText<span style="color:#808030; ">(</span>cell<span style="color:#808030; ">.</span><span style="color:#400000; ">arg</span><span style="color:#808030; ">(</span>row<span style="color:#808030; ">+</span><span style="color:#008c00; ">1</span><span style="color:#808030; ">)</span><span style="color:#808030; ">.</span><span style="color:#400000; ">arg</span><span style="color:#808030; ">(</span>col<span style="color:#808030; ">+</span><span style="color:#008c00; ">1</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

<span style="color:#800080; ">}</span>

<span style="color:#800000; font-weight:bold; ">void</span> SpreadSheet<span style="color:#800080; ">::</span>makeConnexions<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
    <span style="color:#696969; ">//connexion for the sselect all action</span>
    <span style="color:#400000; ">connect</span><span style="color:#808030; ">(</span>all<span style="color:#808030; ">,</span> <span style="color:#808030; ">&amp;</span><span style="color:#603000; ">QAction</span><span style="color:#800080; ">::</span>triggered<span style="color:#808030; ">,</span> spreadsheet<span style="color:#808030; ">,</span> <span style="color:#808030; ">&amp;</span><span style="color:#603000; ">QTableWidget</span><span style="color:#800080; ">::</span>selectAll<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//connection for the show grid</span>
    <span style="color:#400000; ">connect</span><span style="color:#808030; ">(</span>showGrid<span style="color:#808030; ">,</span> <span style="color:#808030; ">&amp;</span><span style="color:#603000; ">QAction</span><span style="color:#800080; ">::</span>triggered<span style="color:#808030; ">,</span>spreadsheet<span style="color:#808030; ">,</span> <span style="color:#808030; ">&amp;</span><span style="color:#603000; ">QTableWidget</span><span style="color:#800080; ">::</span>setShowGrid<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//connection for about</span>
    <span style="color:#400000; ">connect</span><span style="color:#808030; ">(</span>about<span style="color:#808030; ">,</span><span style="color:#808030; ">&amp;</span><span style="color:#603000; ">QAction</span><span style="color:#800080; ">::</span>triggered<span style="color:#808030; ">,</span><span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">,</span><span style="color:#808030; ">&amp;</span>SpreadSheet<span style="color:#800080; ">::</span>help<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//connection for the exit button</span>
    <span style="color:#400000; ">connect</span><span style="color:#808030; ">(</span>quit<span style="color:#808030; ">,</span> <span style="color:#808030; ">&amp;</span><span style="color:#603000; ">QAction</span><span style="color:#800080; ">::</span>triggered<span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">,</span> <span style="color:#808030; ">&amp;</span>SpreadSheet<span style="color:#800080; ">::</span>close<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//conectting the chane of any element in the spreadsheet with the update</span>
    <span style="color:#400000; ">connect</span><span style="color:#808030; ">(</span>spreadsheet<span style="color:#808030; ">,</span> <span style="color:#808030; ">&amp;</span><span style="color:#603000; ">QTableWidget</span><span style="color:#800080; ">::</span>cellClicked<span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span> <span style="color:#808030; ">,</span> <span style="color:#808030; ">&amp;</span>SpreadSheet<span style="color:#800080; ">::</span>updateStatusBar<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//connetion for new file</span>
    <span style="color:#400000; ">connect</span><span style="color:#808030; ">(</span>newFile<span style="color:#808030; ">,</span><span style="color:#808030; ">&amp;</span><span style="color:#603000; ">QAction</span><span style="color:#800080; ">::</span>triggered<span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">,</span> <span style="color:#808030; ">&amp;</span>SpreadSheet<span style="color:#800080; ">::</span>newFil<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//connecter l'action goCell au slot goCellSlot</span>
    <span style="color:#400000; ">connect</span><span style="color:#808030; ">(</span>goCell<span style="color:#808030; ">,</span> <span style="color:#808030; ">&amp;</span><span style="color:#603000; ">QAction</span><span style="color:#800080; ">::</span>triggered<span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span> <span style="color:#808030; ">,</span> <span style="color:#808030; ">&amp;</span>SpreadSheet<span style="color:#800080; ">::</span>goCellSlot<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//connexion between the find and the slot</span>
    <span style="color:#400000; ">connect</span><span style="color:#808030; ">(</span><span style="color:#603000; ">find</span> <span style="color:#808030; ">,</span> <span style="color:#808030; ">&amp;</span><span style="color:#603000; ">QAction</span><span style="color:#800080; ">::</span>triggered<span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span> <span style="color:#808030; ">,</span> <span style="color:#808030; ">&amp;</span>SpreadSheet<span style="color:#800080; ">::</span>findCellSlot<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//connection for delete</span>
     <span style="color:#400000; ">connect</span><span style="color:#808030; ">(</span>deleteAction<span style="color:#808030; ">,</span><span style="color:#808030; ">&amp;</span><span style="color:#603000; ">QAction</span><span style="color:#800080; ">::</span>triggered<span style="color:#808030; ">,</span><span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">,</span><span style="color:#808030; ">&amp;</span>SpreadSheet<span style="color:#800080; ">::</span>deleteItem<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//connection for recent</span>
    <span style="color:#400000; ">connect</span><span style="color:#808030; ">(</span>recent<span style="color:#808030; ">,</span><span style="color:#808030; ">&amp;</span><span style="color:#603000; ">QAction</span><span style="color:#800080; ">::</span>triggered<span style="color:#808030; ">,</span><span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">,</span><span style="color:#808030; ">&amp;</span>SpreadSheet<span style="color:#800080; ">::</span>recentFile<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//connecter les save action a saveSlot</span>
    <span style="color:#400000; ">connect</span><span style="color:#808030; ">(</span>saveFile<span style="color:#808030; ">,</span> <span style="color:#808030; ">&amp;</span><span style="color:#603000; ">QAction</span><span style="color:#800080; ">::</span>triggered<span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span> <span style="color:#808030; ">,</span> <span style="color:#808030; ">&amp;</span>SpreadSheet<span style="color:#800080; ">::</span>saveSlot<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//connecter le save as file</span>
    <span style="color:#400000; ">connect</span><span style="color:#808030; ">(</span>saveasfile<span style="color:#808030; ">,</span><span style="color:#808030; ">&amp;</span><span style="color:#603000; ">QAction</span><span style="color:#800080; ">::</span>triggered<span style="color:#808030; ">,</span><span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">,</span><span style="color:#808030; ">&amp;</span>SpreadSheet<span style="color:#800080; ">::</span>saveAs<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//cennecter le open file</span>
    <span style="color:#400000; ">connect</span><span style="color:#808030; ">(</span>open<span style="color:#808030; ">,</span> <span style="color:#808030; ">&amp;</span><span style="color:#603000; ">QAction</span><span style="color:#800080; ">::</span>triggered<span style="color:#808030; ">,</span> <span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">,</span> <span style="color:#808030; ">&amp;</span>SpreadSheet<span style="color:#800080; ">::</span>loadslot<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

    <span style="color:#696969; ">//connection for row and column</span>
    <span style="color:#400000; ">connect</span><span style="color:#808030; ">(</span>row<span style="color:#808030; ">,</span><span style="color:#808030; ">&amp;</span><span style="color:#603000; ">QAction</span><span style="color:#800080; ">::</span>triggered<span style="color:#808030; ">,</span><span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">,</span><span style="color:#808030; ">&amp;</span>SpreadSheet<span style="color:#800080; ">::</span>selrow<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#400000; ">connect</span><span style="color:#808030; ">(</span>column<span style="color:#808030; ">,</span><span style="color:#808030; ">&amp;</span><span style="color:#603000; ">QAction</span><span style="color:#800080; ">::</span>triggered<span style="color:#808030; ">,</span><span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">,</span><span style="color:#808030; ">&amp;</span>SpreadSheet<span style="color:#800080; ">::</span>selcol<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>

<span style="color:#800000; font-weight:bold; ">void</span> SpreadSheet<span style="color:#800080; ">::</span>goCellSlot<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
<span style="color:#696969; ">//    //Bar d'outi</span>
<span style="color:#696969; ">//    statusBar()-&gt;showMessage("Go Dialog", 2000);</span>

<span style="color:#696969; ">//    //Creer le dialogue</span>
<span style="color:#696969; ">//    GoDialog D;</span>

<span style="color:#696969; ">//    //Executer le dialogue</span>
<span style="color:#696969; ">//    auto reply = D.exec();</span>

<span style="color:#696969; ">//    //si accepter on traite le texte</span>
<span style="color:#696969; ">//    if (reply == QDialog::Accepted){</span>
<span style="color:#696969; ">//        QString cell=D.getCell();</span>

<span style="color:#696969; ">//        int row = cell[0].toLatin1() - 'A';</span>

<span style="color:#696969; ">//        //obtenir la colonne</span>
<span style="color:#696969; ">//        cell = cell.remove(0,1);</span>
<span style="color:#696969; ">//        int col = cell.toInt()-1;</span>

<span style="color:#696969; ">//        spreadsheet-&gt;setCurrentCell(row, col);</span>
<span style="color:#696969; ">//    }</span>
<span style="color:#696969; ">//--------------------------------------------------------</span>
<span style="color:#696969; ">//    findDialog F;</span>
<span style="color:#696969; ">//    int b=0;</span>
<span style="color:#696969; ">//    int row=0 , col=0;</span>
<span style="color:#696969; ">//    statusBar()-&gt; showMessage("finddialog", 2000);</span>
<span style="color:#696969; ">//    auto reply=F.exec();</span>
<span style="color:#696969; ">//    if(reply== QDialog::Accepted){</span>
<span style="color:#696969; ">//        QString cell=F.getCel();</span>
<span style="color:#696969; ">//        for (auto i=0;i&lt;spreadsheet-&gt;rowCount()-1; i++){</span>
<span style="color:#696969; ">//            if(b&gt;0)</span>
<span style="color:#696969; ">//                break;</span>
<span style="color:#696969; ">//            for(auto j=0; j&lt;spreadsheet-&gt;columnCount()-1; j++){</span>
<span style="color:#696969; ">//                if(spreadsheet-&gt;item(i,j) != NULL){</span>
<span style="color:#696969; ">//                    if(spreadsheet-&gt;item(i,j)-&gt;text()==cell){</span>
<span style="color:#696969; ">//                        b++;</span>
<span style="color:#696969; ">//                        row=i;</span>
<span style="color:#696969; ">//                        col=j;</span>
<span style="color:#696969; ">//                        spreadsheet-&gt;setCurrentCell(row, col);</span>
<span style="color:#696969; ">//                    }</span>
<span style="color:#696969; ">//                }</span>
<span style="color:#696969; ">//            }</span>
<span style="color:#696969; ">//        }</span>
<span style="color:#696969; ">//    }</span>
<span style="color:#696969; ">//----------------------------------------------------------</span>
    GoDialog D<span style="color:#800080; ">;</span>
    statusBar<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>showMessage<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">Godialog</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span> <span style="color:#008c00; ">2000</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">auto</span> reply<span style="color:#808030; ">=</span>D<span style="color:#808030; ">.</span>exec<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span>reply<span style="color:#808030; ">=</span><span style="color:#808030; ">=</span><span style="color:#603000; ">QDialog</span><span style="color:#800080; ">::</span>Accepted<span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
        <span style="color:#603000; ">QString</span> cell<span style="color:#808030; ">=</span>D<span style="color:#808030; ">.</span>getCell<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
        <span style="color:#800000; font-weight:bold; ">int</span> row <span style="color:#808030; ">=</span> cell<span style="color:#808030; ">[</span><span style="color:#008c00; ">0</span><span style="color:#808030; ">]</span><span style="color:#808030; ">.</span>toLatin1<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">-</span><span style="color:#0000e6; ">'A'</span><span style="color:#800080; ">;</span>
        cell<span style="color:#808030; ">=</span> cell<span style="color:#808030; ">.</span><span style="color:#603000; ">remove</span><span style="color:#808030; ">(</span><span style="color:#008c00; ">0</span><span style="color:#808030; ">,</span><span style="color:#008c00; ">1</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
        <span style="color:#800000; font-weight:bold; ">int</span> col<span style="color:#808030; ">=</span> cell<span style="color:#808030; ">.</span>toInt<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">-</span><span style="color:#008c00; ">1</span><span style="color:#800080; ">;</span>
        spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setCurrentCell<span style="color:#808030; ">(</span>row<span style="color:#808030; ">,</span> col<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#800080; ">}</span>

<span style="color:#800080; ">}</span>
<span style="color:#800000; font-weight:bold; ">void</span> SpreadSheet<span style="color:#800080; ">::</span>help<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
    <span style="color:#603000; ">QMessageBox</span><span style="color:#800080; ">::</span>about<span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">,</span> <span style="color:#800000; ">"</span><span style="color:#0000e6; ">About Us</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span> <span style="color:#800000; ">"</span><span style="color:#0000e6; ">Filiere de Web et Mobile</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>

<span style="color:#800000; font-weight:bold; ">void</span> SpreadSheet<span style="color:#800080; ">::</span>createMenus<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
    <span style="color:#696969; ">//creer un menu</span>
<span style="color:#696969; ">//    file = menuBar()-&gt;addMenu("&amp;File");</span>
<span style="color:#696969; ">//    file-&gt;addAction(quit);</span>
<span style="color:#696969; ">//    file-&gt;addAction(newFile);</span>
<span style="color:#696969; ">//    file-&gt;addAction(saveFile);</span>
<span style="color:#696969; ">//    file-&gt;addAction(open);</span>
<span style="color:#696969; ">//    file-&gt;addAction(saveasfile);</span>
<span style="color:#696969; ">//    file-&gt;addSeparator();</span>



<span style="color:#696969; ">//    edit = menuBar()-&gt;addMenu("&amp;Edit");</span>
<span style="color:#696969; ">//    edit-&gt;addAction(cut);</span>
<span style="color:#696969; ">//    edit-&gt;addAction(copy);</span>
<span style="color:#696969; ">//    edit-&gt;addAction(paste);</span>
<span style="color:#696969; ">//    edit-&gt;addAction(deleteAction);</span>
<span style="color:#696969; ">//    edit-&gt;addSeparator();</span>
<span style="color:#696969; ">//    edit-&gt;addMenu(select);</span>
<span style="color:#696969; ">//    edit-&gt;addSeparator();</span>
<span style="color:#696969; ">//    edit-&gt;addAction(find);</span>
<span style="color:#696969; ">//    edit-&gt;addAction(goCell);</span>
<span style="color:#696969; ">//    edit-&gt;addAction(recent);</span>

<span style="color:#696969; ">//    auto select =edit-&gt;addMenu("&amp;Select");</span>
<span style="color:#696969; ">//    select-&gt;addAction(row);</span>
<span style="color:#696969; ">//    select-&gt;addAction(column);</span>
<span style="color:#696969; ">//    select-&gt;addAction(all);</span>


<span style="color:#696969; ">//    helpe=menuBar()-&gt;addMenu("&amp;Help");</span>
<span style="color:#696969; ">//    helpe-&gt;addAction(about);</span>

<span style="color:#696969; ">//    optionsMenu = menuBar()-&gt;addMenu("&amp;Option");</span>
<span style="color:#696969; ">//    optionsMenu-&gt;addAction(showGrid);</span>



<span style="color:#696969; ">//    toolsMenu = menuBar()-&gt;addMenu("&amp;Tool");</span>
<span style="color:#696969; ">//    toolsMenu-&gt;addAction(recalculate);</span>
<span style="color:#696969; ">//    toolsMenu-&gt;addAction(sort);</span>
<span style="color:#696969; ">//      file=menuBar()-&gt;addMenu("&amp;file");</span>
<span style="color:#696969; ">//      file-&gt;addAction(newFile);</span>
<span style="color:#696969; ">//      file-&gt;addAction(open);</span>
<span style="color:#696969; ">//      file-&gt;addAction(saveFile);</span>
<span style="color:#696969; ">//      file-&gt;addAction(saveasfile);</span>
<span style="color:#696969; ">//      file-&gt;addSeparator();</span>
<span style="color:#696969; ">//      file-&gt;addAction(quit);</span>

<span style="color:#696969; ">//      edit = new QMenu("&amp;edit");</span>
<span style="color:#696969; ">//      edit-&gt;addAction(cut);</span>
<span style="color:#696969; ">//      edit-&gt;addAction(copy);</span>
<span style="color:#696969; ">//      edit-&gt;addAction(paste);</span>
<span style="color:#696969; ">//      edit-&gt;addAction(recent);</span>
<span style="color:#696969; ">//      edit-&gt;addAction(deleteAction);</span>
<span style="color:#696969; ">//      edit-&gt;addSeparator();</span>
<span style="color:#696969; ">//      edit-&gt;addAction(find);</span>
<span style="color:#696969; ">//      edit-&gt;addAction(goCell);</span>
<span style="color:#696969; ">//      edit-&gt;addMenu(select);</span>

<span style="color:#696969; ">//      menuBar()-&gt;addMenu(edit);</span>

<span style="color:#696969; ">//      select = new QMenu("&amp;select");</span>
<span style="color:#696969; ">//      select-&gt;addAction(row);</span>
<span style="color:#696969; ">//      select-&gt;addAction(column);</span>
<span style="color:#696969; ">//      select-&gt;addAction(all);</span>

<span style="color:#696969; ">//      toolsMenu = new QMenu("&amp;tool");</span>
<span style="color:#696969; ">//      toolsMenu-&gt;addAction(recalculate);</span>
<span style="color:#696969; ">//      toolsMenu-&gt;addAction(sort);</span>

<span style="color:#696969; ">//      menuBar()-&gt;addMenu(toolsMenu);</span>

<span style="color:#696969; ">//      optionsMenu = new QMenu("&amp;options");</span>
<span style="color:#696969; ">//      optionsMenu-&gt;addAction(showGrid);</span>

<span style="color:#696969; ">//      menuBar()-&gt;addMenu(optionsMenu);</span>

<span style="color:#696969; ">//      helpe = new QMenu("&amp;Help");</span>
<span style="color:#696969; ">//      helpe-&gt;addAction(about);</span>

<span style="color:#696969; ">//      menuBar()-&gt;addMenu(helpe);</span>

         file<span style="color:#808030; ">=</span>menuBar<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addMenu<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">&amp;file</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         file<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addAction<span style="color:#808030; ">(</span>newFile<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         file<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addAction<span style="color:#808030; ">(</span>open<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         file<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addAction<span style="color:#808030; ">(</span>saveFile<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         file<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addAction<span style="color:#808030; ">(</span>saveasfile<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         file<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addSeparator<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         file<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addAction<span style="color:#808030; ">(</span>quit<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         edit <span style="color:#808030; ">=</span> <span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QMenu</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">&amp;edit</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         edit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addAction<span style="color:#808030; ">(</span>cut<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         edit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addAction<span style="color:#808030; ">(</span><span style="color:#603000; ">copy</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         edit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addAction<span style="color:#808030; ">(</span>paste<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         edit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addAction<span style="color:#808030; ">(</span>recent<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         edit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addAction<span style="color:#808030; ">(</span>deleteAction<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>


         <span style="color:#400000; ">select</span> <span style="color:#808030; ">=</span> <span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QMenu</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">&amp;select</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         <span style="color:#400000; ">select</span><span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addAction<span style="color:#808030; ">(</span>row<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         <span style="color:#400000; ">select</span><span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addAction<span style="color:#808030; ">(</span>column<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         <span style="color:#400000; ">select</span><span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addAction<span style="color:#808030; ">(</span>all<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         edit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addMenu<span style="color:#808030; ">(</span><span style="color:#400000; ">select</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         edit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addSeparator<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

         edit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addAction<span style="color:#808030; ">(</span><span style="color:#603000; ">find</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         edit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addAction<span style="color:#808030; ">(</span>goCell<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>


         menuBar<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addMenu<span style="color:#808030; ">(</span>edit<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

         toolsMenu <span style="color:#808030; ">=</span> <span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QMenu</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">&amp;tool</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         toolsMenu<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addAction<span style="color:#808030; ">(</span>recalculate<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         toolsMenu<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addAction<span style="color:#808030; ">(</span><span style="color:#603000; ">sort</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         menuBar<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addMenu<span style="color:#808030; ">(</span>toolsMenu<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         optionsMenu <span style="color:#808030; ">=</span> <span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QMenu</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">&amp;options</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         optionsMenu<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addAction<span style="color:#808030; ">(</span>showGrid<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

         menuBar<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addMenu<span style="color:#808030; ">(</span>optionsMenu<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

         helpe <span style="color:#808030; ">=</span> <span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QMenu</span><span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">&amp;Help</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         helpe<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addAction<span style="color:#808030; ">(</span>about<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
         menuBar<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>addMenu<span style="color:#808030; ">(</span>helpe<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>
<span style="color:#800000; font-weight:bold; ">void</span> SpreadSheet<span style="color:#800080; ">::</span>setupMainWindget<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
    spreadsheet<span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QTableWidget</span><span style="color:#800080; ">;</span>
    spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setRowCount<span style="color:#808030; ">(</span><span style="color:#008c00; ">100</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setColumnCount<span style="color:#808030; ">(</span><span style="color:#008c00; ">10</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    setCentralWidget<span style="color:#808030; ">(</span>spreadsheet<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>

<span style="color:#800000; font-weight:bold; ">void</span> SpreadSheet<span style="color:#800080; ">::</span>recentFile<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
    Recent R<span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QString</span> S<span style="color:#800080; ">;</span>

    <span style="color:#800000; font-weight:bold; ">for</span> <span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">auto</span> j<span style="color:#808030; ">=</span><span style="color:#008c00; ">0</span><span style="color:#800080; ">;</span>j<span style="color:#808030; ">&lt;</span>List<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>length<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span> <span style="color:#800080; ">;</span>j<span style="color:#808030; ">+</span><span style="color:#808030; ">+</span> <span style="color:#808030; ">)</span> <span style="color:#800080; ">{</span>
       S<span style="color:#808030; ">+</span><span style="color:#808030; ">=</span>List<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>at<span style="color:#808030; ">(</span>j<span style="color:#808030; ">)</span><span style="color:#808030; ">+</span><span style="color:#800000; ">"</span><span style="color:#0f69ff; ">\n</span><span style="color:#800000; ">"</span><span style="color:#800080; ">;</span>
    <span style="color:#800080; ">}</span>

    R<span style="color:#808030; ">.</span>recentText<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setText<span style="color:#808030; ">(</span>S<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    R<span style="color:#808030; ">.</span>exec<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#800080; ">}</span>

<span style="color:#800000; font-weight:bold; ">void</span> SpreadSheet<span style="color:#800080; ">::</span>newFil<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
    spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>clear<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    currentFile<span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">nullptr</span><span style="color:#800080; ">;</span>
    setWindowTitle<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">buffer</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>
<span style="color:#800000; font-weight:bold; ">void</span> SpreadSheet<span style="color:#800080; ">::</span>findCellSlot<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
    findDialog F<span style="color:#800080; ">;</span>
      <span style="color:#800000; font-weight:bold; ">int</span> b<span style="color:#808030; ">=</span><span style="color:#008c00; ">0</span><span style="color:#800080; ">;</span>
      <span style="color:#800000; font-weight:bold; ">int</span> row<span style="color:#808030; ">=</span><span style="color:#008c00; ">0</span> <span style="color:#808030; ">,</span> col<span style="color:#808030; ">=</span><span style="color:#008c00; ">0</span><span style="color:#800080; ">;</span>
      statusBar<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span> showMessage<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">Find Dialog</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span> <span style="color:#008c00; ">2000</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
      <span style="color:#800000; font-weight:bold; ">auto</span> reply<span style="color:#808030; ">=</span>F<span style="color:#808030; ">.</span>exec<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
      <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span>reply<span style="color:#808030; ">=</span><span style="color:#808030; ">=</span> <span style="color:#603000; ">QDialog</span><span style="color:#800080; ">::</span>Accepted<span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
          <span style="color:#603000; ">QString</span> cell<span style="color:#808030; ">=</span>F<span style="color:#808030; ">.</span>getCel<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
          <span style="color:#800000; font-weight:bold; ">for</span> <span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">auto</span> i<span style="color:#808030; ">=</span><span style="color:#008c00; ">0</span><span style="color:#800080; ">;</span>i<span style="color:#808030; ">&lt;</span>spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>rowCount<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">-</span><span style="color:#008c00; ">1</span><span style="color:#800080; ">;</span> i<span style="color:#808030; ">+</span><span style="color:#808030; ">+</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
              <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span>b<span style="color:#808030; ">&gt;</span><span style="color:#008c00; ">0</span><span style="color:#808030; ">)</span>
                  <span style="color:#800000; font-weight:bold; ">break</span><span style="color:#800080; ">;</span>
              <span style="color:#800000; font-weight:bold; ">for</span><span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">auto</span> j<span style="color:#808030; ">=</span><span style="color:#008c00; ">0</span><span style="color:#800080; ">;</span> j<span style="color:#808030; ">&lt;</span>spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>columnCount<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">-</span><span style="color:#008c00; ">1</span><span style="color:#800080; ">;</span> j<span style="color:#808030; ">+</span><span style="color:#808030; ">+</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
                  <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span>spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>item<span style="color:#808030; ">(</span>i<span style="color:#808030; ">,</span>j<span style="color:#808030; ">)</span> <span style="color:#808030; ">!</span><span style="color:#808030; ">=</span> <span style="color:#7d0045; ">NULL</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
                      <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span>spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>item<span style="color:#808030; ">(</span>i<span style="color:#808030; ">,</span>j<span style="color:#808030; ">)</span><span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>text<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">=</span><span style="color:#808030; ">=</span>cell<span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
                          b<span style="color:#808030; ">+</span><span style="color:#808030; ">+</span><span style="color:#800080; ">;</span>
                          row<span style="color:#808030; ">=</span>i<span style="color:#800080; ">;</span>
                          col<span style="color:#808030; ">=</span>j<span style="color:#800080; ">;</span>
                          spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setCurrentCell<span style="color:#808030; ">(</span>row<span style="color:#808030; ">,</span> col<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
                      <span style="color:#800080; ">}</span>
                  <span style="color:#800080; ">}</span>
              <span style="color:#800080; ">}</span>
          <span style="color:#800080; ">}</span>
      <span style="color:#800080; ">}</span>
      <span style="color:#696969; ">//---------------------------------------------------------------</span>
<span style="color:#696969; ">//    statusBar()-&gt;showMessage("Searching for an expression",3000);</span>

<span style="color:#696969; ">//    //getting the dialog</span>
<span style="color:#696969; ">//    findDialog D;</span>
<span style="color:#696969; ">//    auto reply =D.exec();</span>
<span style="color:#696969; ">//    if(reply == QDialog::Accepted)</span>
<span style="color:#696969; ">//{</span>
<span style="color:#696969; ">//        auto pattern = D.getCel();</span>

<span style="color:#696969; ">//        for (int i=0 ; i&lt;spreadsheet-&gt;rowCount(); i++)</span>
<span style="color:#696969; ">//            for(int j= 0; j&lt; spreadsheet-&gt;colorCount(); j++)</span>
<span style="color:#696969; ">//            {</span>

<span style="color:#696969; ">//                //getting the content fo the cell</span>
<span style="color:#696969; ">//                auto content = spreadsheet-&gt;item(i,j);</span>
<span style="color:#696969; ">//                if(content)</span>
<span style="color:#696969; ">//                {</span>

<span style="color:#696969; ">//                    //serching</span>
<span style="color:#696969; ">//                    if(content -&gt;text().contains(pattern))</span>
<span style="color:#696969; ">//                    {</span>
<span style="color:#696969; ">//                        spreadsheet-&gt; setCurrentCell(i,j);</span>
<span style="color:#696969; ">//                        return;</span>
<span style="color:#696969; ">//                    }</span>
<span style="color:#696969; ">//                }</span>
<span style="color:#696969; ">//            }</span>
<span style="color:#696969; ">//    }</span>
<span style="color:#800080; ">}</span>

<span style="color:#800000; font-weight:bold; ">void</span> SpreadSheet<span style="color:#800080; ">::</span>saveSlot<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
<span style="color:#696969; ">//    //1 tester si j'ai un nom de fichier (currentFile)</span>
<span style="color:#696969; ">//    if(!currentFile)</span>
<span style="color:#696969; ">//    {</span>

<span style="color:#696969; ">//        //2 au cas ou non Obtenir lle nom du fichier</span>
<span style="color:#696969; ">//        QFileDialog D;</span>
<span style="color:#696969; ">//        auto filename =D.getSaveFileName();</span>
<span style="color:#696969; ">//        currentFile = new QString(filename);</span>
<span style="color:#696969; ">//        setWindowTitle(filename);</span>

<span style="color:#696969; ">//        //Sauvegarder le contenu</span>
<span style="color:#696969; ">//        saveContent(filename);</span>
<span style="color:#696969; ">//    }</span>
<span style="color:#696969; ">//---------------------------------------------------------------</span>
    <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span><span style="color:#808030; ">!</span>currentFile<span style="color:#808030; ">)</span>
    <span style="color:#800080; ">{</span>
        <span style="color:#603000; ">QFileDialog</span> D<span style="color:#800080; ">;</span>
        <span style="color:#696969; ">//QTextStream out(stdout);</span>
        <span style="color:#800000; font-weight:bold; ">auto</span> filename<span style="color:#808030; ">=</span>D<span style="color:#808030; ">.</span>getSaveFileName<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
        currentFile<span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QString</span><span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
        k<span style="color:#808030; ">+</span><span style="color:#808030; ">+</span><span style="color:#800080; ">;</span>
        <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span>k<span style="color:#808030; ">&lt;</span><span style="color:#808030; ">=</span><span style="color:#008c00; ">5</span><span style="color:#808030; ">)</span>
          List<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>append<span style="color:#808030; ">(</span><span style="color:#808030; ">*</span>currentFile<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>

        setWindowTitle<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
        saveContent<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#800080; ">}</span>
<span style="color:#800080; ">}</span>

<span style="color:#800000; font-weight:bold; ">void</span> SpreadSheet<span style="color:#800080; ">::</span>saveAs<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
    <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span>currentFile<span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
        <span style="color:#603000; ">QFileDialog</span> D<span style="color:#800080; ">;</span>
        <span style="color:#800000; font-weight:bold; ">auto</span> filename<span style="color:#808030; ">=</span>D<span style="color:#808030; ">.</span>getSaveFileName<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
        currentFile<span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QString</span><span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
        k<span style="color:#808030; ">+</span><span style="color:#808030; ">+</span><span style="color:#800080; ">;</span>
        <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span>k<span style="color:#808030; ">&lt;</span><span style="color:#808030; ">=</span><span style="color:#008c00; ">5</span><span style="color:#808030; ">)</span>
           List<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>append<span style="color:#808030; ">(</span><span style="color:#808030; ">*</span>currentFile<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
        setWindowTitle<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
        saveAsContent<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#800080; ">}</span>
<span style="color:#800080; ">}</span>
<span style="color:#800000; font-weight:bold; ">void</span> SpreadSheet<span style="color:#800080; ">::</span>loadslot<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
    <span style="color:#603000; ">QFileDialog</span> D<span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">auto</span> filename<span style="color:#808030; ">=</span>D<span style="color:#808030; ">.</span>getOpenFileName<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    setWindowTitle<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    loadcontent<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QString</span> s<span style="color:#800080; ">;</span>
        <span style="color:#800000; font-weight:bold; ">for</span> <span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">auto</span> i<span style="color:#808030; ">=</span>filename<span style="color:#808030; ">.</span>length<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">-</span><span style="color:#008c00; ">3</span><span style="color:#800080; ">;</span>i<span style="color:#808030; ">&lt;</span><span style="color:#808030; ">=</span>filename<span style="color:#808030; ">.</span>length<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">-</span><span style="color:#008c00; ">1</span><span style="color:#800080; ">;</span>i<span style="color:#808030; ">+</span><span style="color:#808030; ">+</span><span style="color:#808030; ">)</span>
        <span style="color:#800080; ">{</span>
           s<span style="color:#808030; ">+</span><span style="color:#808030; ">=</span>filename<span style="color:#808030; ">.</span>at<span style="color:#808030; ">(</span>i<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
        <span style="color:#800080; ">}</span>
        <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span>s<span style="color:#808030; ">=</span><span style="color:#808030; ">=</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">csv</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span>
           loadContCSV<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
        <span style="color:#800000; font-weight:bold; ">else</span>
           loadcontent<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>

<span style="color:#800000; font-weight:bold; ">void</span> SpreadSheet<span style="color:#800080; ">::</span>deleteItem<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
    <span style="color:#800000; font-weight:bold; ">int</span> a<span style="color:#808030; ">,</span>b<span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QTableWidgetItem</span> <span style="color:#808030; ">*</span>newItem <span style="color:#808030; ">=</span> <span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QTableWidgetItem</span><span style="color:#808030; ">(</span><span style="color:#603000; ">QString</span><span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    a<span style="color:#808030; ">=</span>spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>currentColumn<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    b<span style="color:#808030; ">=</span>spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>currentRow<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setItem<span style="color:#808030; ">(</span>a<span style="color:#808030; ">,</span>b<span style="color:#808030; ">,</span>newItem<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>
<span style="color:#800000; font-weight:bold; ">void</span> SpreadSheet<span style="color:#800080; ">::</span>saveContent<span style="color:#808030; ">(</span><span style="color:#603000; ">QString</span> filename<span style="color:#808030; ">)</span> <span style="color:#800000; font-weight:bold; ">const</span><span style="color:#800080; ">{</span>

    <span style="color:#696969; ">//ouvrir un pointeur sur le fichier</span>
    <span style="color:#603000; ">QFile</span> file<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>


    <span style="color:#696969; ">//en C === FILE * FID = fopen(filename, "r")</span>

    <span style="color:#696969; ">//tester si je peux ouvrir le fichier</span>
    <span style="color:#800000; font-weight:bold; ">if</span> <span style="color:#808030; ">(</span>file<span style="color:#808030; ">.</span>open<span style="color:#808030; ">(</span><span style="color:#603000; ">QIODevice</span><span style="color:#800080; ">::</span>WriteOnly<span style="color:#808030; ">)</span><span style="color:#808030; ">)</span>
    <span style="color:#800080; ">{</span>
      <span style="color:#696969; ">//flux d'information sur ce fichier</span>
      <span style="color:#603000; ">QTextStream</span> out<span style="color:#808030; ">(</span><span style="color:#808030; ">&amp;</span>file<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
      <span style="color:#800000; font-weight:bold; ">for</span> <span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">int</span> i<span style="color:#808030; ">=</span><span style="color:#008c00; ">0</span> <span style="color:#800080; ">;</span> i<span style="color:#808030; ">&lt;</span><span style="color:#808030; ">=</span>spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>rowCount<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>i<span style="color:#808030; ">+</span><span style="color:#808030; ">+</span><span style="color:#808030; ">)</span>
          <span style="color:#800000; font-weight:bold; ">for</span><span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">int</span> j<span style="color:#808030; ">=</span><span style="color:#008c00; ">0</span><span style="color:#800080; ">;</span> j<span style="color:#808030; ">&lt;</span><span style="color:#808030; ">=</span> spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>columnCount<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span> j<span style="color:#808030; ">+</span><span style="color:#808030; ">+</span><span style="color:#808030; ">)</span>
          <span style="color:#800080; ">{</span>
              <span style="color:#800000; font-weight:bold; ">auto</span> cell <span style="color:#808030; ">=</span> spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>item<span style="color:#808030; ">(</span>i<span style="color:#808030; ">,</span> j<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
              <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span>cell<span style="color:#808030; ">)</span>
                  out <span style="color:#808030; ">&lt;</span><span style="color:#808030; ">&lt;</span> i <span style="color:#808030; ">&lt;</span><span style="color:#808030; ">&lt;</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">,</span><span style="color:#800000; ">"</span><span style="color:#808030; ">&lt;</span><span style="color:#808030; ">&lt;</span> j <span style="color:#808030; ">&lt;</span><span style="color:#808030; ">&lt;</span> <span style="color:#800000; ">"</span><span style="color:#0000e6; ">, </span><span style="color:#800000; ">"</span> <span style="color:#808030; ">&lt;</span><span style="color:#808030; ">&lt;</span> cell<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>text<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span> <span style="color:#808030; ">&lt;</span><span style="color:#808030; ">&lt;</span> <span style="color:#666616; ">Qt</span><span style="color:#800080; ">::</span><span style="color:#603000; ">endl</span><span style="color:#800080; ">;</span>
          <span style="color:#800080; ">}</span>
    <span style="color:#800080; ">}</span>


    <span style="color:#696969; ">//liberer le pointeur sur la resource</span>
    file<span style="color:#808030; ">.</span>close<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>
<span style="color:#800000; font-weight:bold; ">void</span> SpreadSheet<span style="color:#800080; ">::</span>saveAsContent<span style="color:#808030; ">(</span><span style="color:#603000; ">QString</span> filename<span style="color:#808030; ">)</span> <span style="color:#800000; font-weight:bold; ">const</span><span style="color:#800080; ">{</span>
    <span style="color:#603000; ">QFile</span> file<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#696969; ">//en C === FILE*fid=fopen(filename,"r")</span>
    <span style="color:#696969; ">//test si je peux ouvrir le fichier</span>
    <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span>file<span style="color:#808030; ">.</span>open<span style="color:#808030; ">(</span><span style="color:#603000; ">QIODevice</span><span style="color:#800080; ">::</span>WriteOnly<span style="color:#808030; ">)</span><span style="color:#808030; ">)</span>
    <span style="color:#800080; ">{</span>
           <span style="color:#603000; ">QTextStream</span> out<span style="color:#808030; ">(</span><span style="color:#808030; ">&amp;</span>file<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
           <span style="color:#800000; font-weight:bold; ">for</span> <span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">int</span> i<span style="color:#808030; ">=</span><span style="color:#008c00; ">0</span><span style="color:#800080; ">;</span>i<span style="color:#808030; ">&lt;</span><span style="color:#808030; ">=</span>spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>rowCount<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span> <span style="color:#800080; ">;</span>i<span style="color:#808030; ">+</span><span style="color:#808030; ">+</span> <span style="color:#808030; ">)</span> <span style="color:#800080; ">{</span>
               <span style="color:#800000; font-weight:bold; ">for</span> <span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">int</span> j<span style="color:#808030; ">=</span><span style="color:#008c00; ">0</span><span style="color:#800080; ">;</span>j<span style="color:#808030; ">&lt;</span><span style="color:#808030; ">=</span>spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>colorCount<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span> <span style="color:#800080; ">;</span>j<span style="color:#808030; ">+</span><span style="color:#808030; ">+</span> <span style="color:#808030; ">)</span> <span style="color:#800080; ">{</span>
                   <span style="color:#800000; font-weight:bold; ">auto</span> cell<span style="color:#808030; ">=</span>spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>item<span style="color:#808030; ">(</span>i<span style="color:#808030; ">,</span>j<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
                   <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span>cell<span style="color:#808030; ">)</span>
                       out<span style="color:#808030; ">&lt;</span><span style="color:#808030; ">&lt;</span>i<span style="color:#808030; ">&lt;</span><span style="color:#808030; ">&lt;</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">,</span><span style="color:#800000; ">"</span><span style="color:#808030; ">&lt;</span><span style="color:#808030; ">&lt;</span>j<span style="color:#808030; ">&lt;</span><span style="color:#808030; ">&lt;</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">,</span><span style="color:#800000; ">"</span><span style="color:#808030; ">&lt;</span><span style="color:#808030; ">&lt;</span>cell<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>text<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">&lt;</span><span style="color:#808030; ">&lt;</span><span style="color:#666616; ">Qt</span><span style="color:#800080; ">::</span><span style="color:#603000; ">endl</span><span style="color:#800080; ">;</span>
               <span style="color:#800080; ">}</span>
           <span style="color:#800080; ">}</span>
     <span style="color:#800080; ">}</span>
     file<span style="color:#808030; ">.</span>close<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>

<span style="color:#800000; font-weight:bold; ">void</span> SpreadSheet<span style="color:#800080; ">::</span>loadcontent<span style="color:#808030; ">(</span><span style="color:#603000; ">QString</span> filename<span style="color:#808030; ">)</span> <span style="color:#800000; font-weight:bold; ">const</span><span style="color:#800080; ">{</span>
    <span style="color:#603000; ">QFile</span> file<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>clear<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QString</span> e<span style="color:#808030; ">,</span> c<span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">int</span> row<span style="color:#808030; ">=</span><span style="color:#008c00; ">0</span> <span style="color:#808030; ">,</span> col<span style="color:#808030; ">=</span><span style="color:#008c00; ">0</span> <span style="color:#808030; ">,</span> d<span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QString</span> A<span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span>file<span style="color:#808030; ">.</span>open<span style="color:#808030; ">(</span><span style="color:#603000; ">QIODevice</span><span style="color:#800080; ">::</span>ReadOnly<span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
        <span style="color:#603000; ">QTextStream</span> in<span style="color:#808030; ">(</span><span style="color:#808030; ">&amp;</span>file<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
        <span style="color:#603000; ">QString</span> text<span style="color:#808030; ">=</span> in<span style="color:#808030; ">.</span>readAll<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
        d<span style="color:#808030; ">=</span><span style="color:#008c00; ">0</span><span style="color:#800080; ">;</span>
        <span style="color:#800000; font-weight:bold; ">for</span><span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">auto</span> i<span style="color:#808030; ">=</span><span style="color:#008c00; ">0</span><span style="color:#800080; ">;</span> i<span style="color:#808030; ">&lt;</span>text<span style="color:#808030; ">.</span>length<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>i<span style="color:#808030; ">+</span><span style="color:#808030; ">+</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
            <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span>d<span style="color:#808030; ">=</span><span style="color:#808030; ">=</span><span style="color:#008c00; ">0</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
                e<span style="color:#808030; ">=</span>text<span style="color:#808030; ">.</span>at<span style="color:#808030; ">(</span>i<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
                row<span style="color:#808030; ">=</span>e<span style="color:#808030; ">.</span>toInt<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
                c<span style="color:#808030; ">=</span>text<span style="color:#808030; ">.</span>at<span style="color:#808030; ">(</span>i<span style="color:#808030; ">+</span><span style="color:#008c00; ">2</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
                col<span style="color:#808030; ">=</span>c<span style="color:#808030; ">.</span>toInt<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
                d<span style="color:#808030; ">+</span><span style="color:#808030; ">+</span><span style="color:#800080; ">;</span>
            <span style="color:#800080; ">}</span>
            <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span>text<span style="color:#808030; ">[</span>i<span style="color:#808030; ">]</span><span style="color:#808030; ">.</span>isLetter<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span>
                A<span style="color:#808030; ">+</span><span style="color:#808030; ">=</span>text<span style="color:#808030; ">[</span>i<span style="color:#808030; ">]</span><span style="color:#800080; ">;</span>
            <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span>text<span style="color:#808030; ">[</span>i<span style="color:#808030; ">]</span><span style="color:#808030; ">=</span><span style="color:#808030; ">=</span><span style="color:#800000; ">"</span><span style="color:#0f69ff; ">\n</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
                <span style="color:#603000; ">QTableWidgetItem</span> <span style="color:#808030; ">*</span>newItem <span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QTableWidgetItem</span><span style="color:#808030; ">(</span>A<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
                spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setItem<span style="color:#808030; ">(</span>row<span style="color:#808030; ">,</span> col<span style="color:#808030; ">,</span> newItem<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
                A<span style="color:#808030; ">.</span>clear<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
                e<span style="color:#808030; ">.</span>clear<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
                c<span style="color:#808030; ">.</span>clear<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
                row<span style="color:#808030; ">=</span><span style="color:#008c00; ">0</span><span style="color:#800080; ">;</span>
                col<span style="color:#808030; ">=</span><span style="color:#008c00; ">0</span><span style="color:#800080; ">;</span>
                d<span style="color:#808030; ">=</span><span style="color:#008c00; ">0</span><span style="color:#800080; ">;</span>
            <span style="color:#800080; ">}</span>
        <span style="color:#800080; ">}</span>
    <span style="color:#800080; ">}</span>
    file<span style="color:#808030; ">.</span>close<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>

<span style="color:#800000; font-weight:bold; ">void</span> SpreadSheet<span style="color:#800080; ">::</span>loadContCSV<span style="color:#808030; ">(</span><span style="color:#603000; ">QString</span> filename<span style="color:#808030; ">)</span> <span style="color:#800000; font-weight:bold; ">const</span><span style="color:#800080; ">{</span>
    <span style="color:#603000; ">QFile</span> file<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>clear<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QString</span> S<span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">int</span> x<span style="color:#808030; ">=</span><span style="color:#008c00; ">0</span><span style="color:#808030; ">,</span>j<span style="color:#808030; ">=</span><span style="color:#008c00; ">0</span><span style="color:#800080; ">;</span>
         <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span>file<span style="color:#808030; ">.</span>open<span style="color:#808030; ">(</span><span style="color:#603000; ">QIODevice</span><span style="color:#800080; ">::</span>ReadOnly<span style="color:#808030; ">)</span><span style="color:#808030; ">)</span>
         <span style="color:#800080; ">{</span>
             <span style="color:#603000; ">QTextStream</span> in<span style="color:#808030; ">(</span><span style="color:#808030; ">&amp;</span>file<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
             <span style="color:#603000; ">QString</span> text<span style="color:#808030; ">=</span>in<span style="color:#808030; ">.</span>readAll<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
             <span style="color:#800000; font-weight:bold; ">for</span> <span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">auto</span> i<span style="color:#808030; ">=</span><span style="color:#008c00; ">0</span><span style="color:#800080; ">;</span>i<span style="color:#808030; ">&lt;</span>text<span style="color:#808030; ">.</span>length<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>i<span style="color:#808030; ">+</span><span style="color:#808030; ">+</span> <span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
                  <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span>text<span style="color:#808030; ">[</span>i<span style="color:#808030; ">]</span><span style="color:#808030; ">.</span>isLetter<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span> <span style="color:#808030; ">|</span><span style="color:#808030; ">|</span>text<span style="color:#808030; ">[</span>i<span style="color:#808030; ">]</span><span style="color:#808030; ">.</span>isDigit<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span> <span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
                      S<span style="color:#808030; ">+</span><span style="color:#808030; ">=</span>text<span style="color:#808030; ">[</span>i<span style="color:#808030; ">]</span><span style="color:#800080; ">;</span>
                  <span style="color:#800080; ">}</span>
                  <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span>text<span style="color:#808030; ">[</span>i<span style="color:#808030; ">]</span><span style="color:#808030; ">.</span>isPunct<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">|</span><span style="color:#808030; ">|</span>text<span style="color:#808030; ">[</span>i<span style="color:#808030; ">]</span><span style="color:#808030; ">=</span><span style="color:#808030; ">=</span><span style="color:#800000; ">"</span><span style="color:#0f69ff; ">\n</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
                      <span style="color:#603000; ">QTableWidgetItem</span> <span style="color:#808030; ">*</span>newItem <span style="color:#808030; ">=</span> <span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QTableWidgetItem</span><span style="color:#808030; ">(</span>S<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
                      spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setItem<span style="color:#808030; ">(</span>j<span style="color:#808030; ">,</span>x<span style="color:#808030; ">,</span>newItem<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
                      x<span style="color:#808030; ">+</span><span style="color:#808030; ">+</span><span style="color:#800080; ">;</span>
                      S<span style="color:#808030; ">.</span>clear<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
                      <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span>text<span style="color:#808030; ">[</span>i<span style="color:#808030; ">]</span><span style="color:#808030; ">=</span><span style="color:#808030; ">=</span><span style="color:#800000; ">"</span><span style="color:#0f69ff; ">\n</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
                         j<span style="color:#808030; ">+</span><span style="color:#808030; ">+</span><span style="color:#800080; ">;</span>
                         x<span style="color:#808030; ">=</span><span style="color:#008c00; ">0</span><span style="color:#800080; ">;</span>
                     <span style="color:#800080; ">}</span>
                  <span style="color:#800080; ">}</span>
              <span style="color:#800080; ">}</span>
         <span style="color:#800080; ">}</span>
         file<span style="color:#808030; ">.</span>close<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>

<span style="color:#800000; font-weight:bold; ">void</span> SpreadSheet<span style="color:#800080; ">::</span>selcol<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
    <span style="color:#800000; font-weight:bold; ">int</span> c<span style="color:#800080; ">;</span>
    c<span style="color:#808030; ">=</span>spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>currentColumn<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>selectColumn<span style="color:#808030; ">(</span>c<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>
<span style="color:#800000; font-weight:bold; ">void</span> SpreadSheet<span style="color:#800080; ">::</span>selrow<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
    <span style="color:#800000; font-weight:bold; ">int</span> r<span style="color:#800080; ">;</span>
    r<span style="color:#808030; ">=</span>spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>currentRow<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    spreadsheet<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>selectRow<span style="color:#808030; ">(</span>r<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>
</pre>

![WhatsApp Image 2021-12-19 at 17 05 16](https://user-images.githubusercontent.com/86810485/146683241-e507b9c8-0238-4598-b4c4-a630e78a3ca3.jpeg)

  # Find Dialog
We will move now for the Find dialog. This dialog prompts the user for a input and seek a cell that contains the entered text.

  * Create a Form Class with the following ui:
  
   ![find_dialog_ui](https://user-images.githubusercontent.com/86810485/146683379-f9fcbb20-7438-4f07-9e2a-1e2413e6cc60.png)
       
                                               Find Dialog ui form.
      
      
                                               
  * Add a Getter to obtain the searched text.
  
  * Implements the connexion between the dialog and the find function:
  
Here is a set of useful information about the class:QTableWidget

 * The method gives the number of rows.rowCount()
 * The method gives the number of columns in the spreadsheet.columnCount()
 * The Method return a pointer on the cell indexed by i and j.item(int i, int j)
 * If this pointer is not null, you could get its content by the method which returns a .text()QString

 * Finally, the method to change the focused cell is
 
             spreadsheet->setCurrentCell(int i, int j);
             

![find_cell_query](https://user-images.githubusercontent.com/86810485/146684517-ef8fd165-112b-4670-b09d-fc854610284e.png)


![find_cell_response](https://user-images.githubusercontent.com/86810485/146684528-e7ebe3b6-8587-46f4-8a87-f5f621669839.png)

                                                 Find dialog illustration

 # Saving and loading files
 
For our final task, we will learn how to save the content of our spreadsheet in a simple format. Since a spreadsheet is not forcefully tabular, it will be a waste to save all the empty data. Hence, we will choose a simple format that store the coordinates and the content of the non empty cells.

       i1, j1, content1
       i2, j2, content2
       .
       .
       .
       
Saving Content

We will start by writing a private function to save the content of our spreadsheet in a text file.saveContent(QSTring filename)

  * We will add the declaration in the file:header

             protected:
                   void saveContent(QString filename)const;
                   
  * For the implementation, we will using two classes:
  
     /  QFile which provides an interface to read and write in files.
     
    /   QTextStream for manipulating objects with a stream such as a file.
    
  * Here is the complete implementation of this function:
   
         void SpreadSheet::saveContent(QString filename) const
         {

          //Gettign a pointer on the file
          QFile file(filename);

          //Openign the file
          if(file.open(QIODevice::WriteOnly))  //Opening the file in writing mode
          {
              //Initiating a stream using the file
              QTextStream out(&file);

              //loop to save all the content
              for(int i=0; i < spreadsheet->rowCount();i++)
                  for(int j=0; j < spreadsheet->columnCount(); j++)
                  {
                      auto cell = spreadsheet->item(i, j);

                      //Cecking if the cell is non empty
                      if(cell)
                      out << cell->row() << ", "<< cell->column() << ", " << cell->text() << endl;
                  }

          }
          file.close();
      }




save File action

Now that we have an operational function, we could focus on the slot itself.saveContent

So first we will create a slot to respond to the action trigger in the header.

          private slots:
               void saveSlot();             //Slot to save the content of the file
               
Now we will add the connexion in the function:makeConnexion

         //Connexion for the saveFile
         connect(save, &QAction::triggered, this, &SpreadSheet::saveSlot);
         
Finally for the interesting part, the implementation of the slot


       void SpreadSheet::saveSlot()
       {
           //Creating a file dialog to choose a file graphically
           auto dialog = new QFileDialog(this);

           //Check if the current file has a name or not
            if(currentFile == "")
            {
              currentFile = dialog->getSaveFileName(this,"choose your file");

               //Update the window title with the file name
              setWindowTitle(currentFile);
            }

          //If we have a name simply save the content
           if( currentFile != "")
          {
                  saveContent(currentFile);
          }
       }

Load File
# finddialog.h

<pre style="color:#000000;background:#ffffff;"><span style="color:#004a43; ">#</span><span style="color:#004a43; ">ifndef</span><span style="color:#004a43; "> FINDDIALOG_H</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">define</span><span style="color:#004a43; "> FINDDIALOG_H</span>

<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">&lt;</span><span style="color:#40015a; ">QDialog</span><span style="color:#800000; ">&gt;</span>

<span style="color:#800000; font-weight:bold; ">namespace</span> Ui <span style="color:#800080; ">{</span>
<span style="color:#800000; font-weight:bold; ">class</span> findDialog<span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>

<span style="color:#800000; font-weight:bold; ">class</span> findDialog <span style="color:#800080; ">:</span> <span style="color:#800000; font-weight:bold; ">public</span> <span style="color:#603000; ">QDialog</span>
<span style="color:#800080; ">{</span>
    <span style="color:#004a43; ">Q_OBJECT</span>

<span style="color:#800000; font-weight:bold; ">public</span><span style="color:#e34adc; ">:</span>
    <span style="color:#800000; font-weight:bold; ">explicit</span> findDialog<span style="color:#808030; ">(</span><span style="color:#603000; ">QWidget</span> <span style="color:#808030; ">*</span>parent <span style="color:#808030; ">=</span> <span style="color:#800000; font-weight:bold; ">nullptr</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#808030; ">~</span>findDialog<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
     <span style="color:#603000; ">QString</span> getCel<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span> <span style="color:#800000; font-weight:bold; ">const</span><span style="color:#800080; ">;</span>

<span style="color:#800000; font-weight:bold; ">private</span><span style="color:#e34adc; ">:</span>
    Ui<span style="color:#800080; ">::</span>findDialog <span style="color:#808030; ">*</span>ui<span style="color:#800080; ">;</span>

<span style="color:#800080; ">}</span><span style="color:#800080; ">;</span>

<span style="color:#004a43; ">#</span><span style="color:#004a43; ">endif</span><span style="color:#004a43; "> </span><span style="color:#696969; ">// FINDDIALOG_H</span>
</pre>


# finddialog.cpp

<pre style="color:#000000;background:#ffffff;"><span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">"</span><span style="color:#40015a; ">finddialog.h</span><span style="color:#800000; ">"</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">"</span><span style="color:#40015a; ">ui_finddialog.h</span><span style="color:#800000; ">"</span>

findDialog<span style="color:#800080; ">::</span>findDialog<span style="color:#808030; ">(</span><span style="color:#603000; ">QWidget</span> <span style="color:#808030; ">*</span>parent<span style="color:#808030; ">)</span> <span style="color:#800080; ">:</span>
    <span style="color:#603000; ">QDialog</span><span style="color:#808030; ">(</span>parent<span style="color:#808030; ">)</span><span style="color:#808030; ">,</span>
    ui<span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">new</span> Ui<span style="color:#800080; ">::</span>findDialog<span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    ui<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setupUi<span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>

findDialog<span style="color:#800080; ">::</span><span style="color:#808030; ">~</span>findDialog<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    <span style="color:#800000; font-weight:bold; ">delete</span> ui<span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>

<span style="color:#603000; ">QString</span> findDialog<span style="color:#800080; ">::</span>getCel<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span> <span style="color:#800000; font-weight:bold; ">const</span><span style="color:#800080; ">{</span>
    <span style="color:#800000; font-weight:bold; ">return</span> ui<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>lineEdit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>text<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>
</pre>

# finddialog.ui
![WhatsApp Image 2021-12-19 at 17 07 50](https://user-images.githubusercontent.com/86810485/146685357-23f19a99-6cec-4f75-a379-b06f02d51bbb.jpeg)

# saving
![WhatsApp Image 2021-12-19 at 17 10 18](https://user-images.githubusercontent.com/86810485/146685484-d1bf2b49-c1ab-466a-a5a8-e807ef21f7be.jpeg)

# open
![WhatsApp Image 2021-12-19 at 17 12 50](https://user-images.githubusercontent.com/86810485/146685511-dfc5eb8f-a9a7-46f0-be95-3a85ddc46755.jpeg)


# Text Editor
For your first example, we will playing the Designer for a fast application creation. The application is from Qt Examples and is a simple text editor program built around QPlainText.

![wordEditor](https://user-images.githubusercontent.com/86810485/146685670-ec84e4ef-2aec-4220-929a-079f9ec28889.png)


We will mainly use the designer for a rapid design of it features. But if you feel adventurous you can write all in using code.

Creating the project.

First create a new project called using the following steps:WordText

  * Choose a .QT Widgets Application
  * Name your project WordText
  * Inherit now from QMainWindow
  * Choose all the remaining default choices.
  * 
Open the form You’ll see that it has now an empty menu and already a defined menu and satatus bars.



# textEdit.h

<pre style="color:#000000;background:#ffffff;"><span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">"</span><span style="color:#40015a; ">texteditor.h</span><span style="color:#800000; ">"</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">"</span><span style="color:#40015a; ">ui_texteditor.h</span><span style="color:#800000; ">"</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include</span><span style="color:#800000; ">"</span><span style="color:#40015a; ">QFileDialog</span><span style="color:#800000; ">"</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include</span><span style="color:#800000; ">"</span><span style="color:#40015a; ">QMessageBox</span><span style="color:#800000; ">"</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include</span><span style="color:#800000; ">"</span><span style="color:#40015a; ">QTextStream</span><span style="color:#800000; ">"</span>

TextEditor<span style="color:#800080; ">::</span>TextEditor<span style="color:#808030; ">(</span><span style="color:#603000; ">QWidget</span> <span style="color:#808030; ">*</span>parent<span style="color:#808030; ">)</span> <span style="color:#800080; ">:</span>
    <span style="color:#603000; ">QDialog</span><span style="color:#808030; ">(</span>parent<span style="color:#808030; ">)</span><span style="color:#808030; ">,</span>
    ui<span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">new</span> Ui<span style="color:#800080; ">::</span>TextEditor<span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    ui<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setupUi<span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#696969; ">//setCentralWidget(ui-&gt;textEdit);</span>
    currentFile<span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">nullptr</span><span style="color:#800080; ">;</span>
    setWindowTitle<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">buffer</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>

TextEditor<span style="color:#800080; ">::</span><span style="color:#808030; ">~</span>TextEditor<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    <span style="color:#800000; font-weight:bold; ">delete</span> ui<span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>

<span style="color:#800000; font-weight:bold; ">void</span> TextEditor<span style="color:#800080; ">::</span>on_actionnew_triggered<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
<span style="color:#696969; ">//    currentFile-&gt;clear();</span>
    ui<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>textEdit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setPlainText<span style="color:#808030; ">(</span><span style="color:#603000; ">QString</span><span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>


<span style="color:#800000; font-weight:bold; ">void</span> TextEditor<span style="color:#800080; ">::</span>on_actionopen_triggered<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    <span style="color:#800000; font-weight:bold; ">auto</span> filename<span style="color:#808030; ">=</span> <span style="color:#603000; ">QFileDialog</span><span style="color:#800080; ">::</span>getOpenFileName<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QFile</span> file<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    currentFile<span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QString</span><span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span><span style="color:#808030; ">!</span>file<span style="color:#808030; ">.</span>open<span style="color:#808030; ">(</span><span style="color:#603000; ">QIODevice</span><span style="color:#800080; ">::</span>ReadOnly <span style="color:#808030; ">|</span> <span style="color:#603000; ">QFile</span><span style="color:#800080; ">::</span>Text<span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
        <span style="color:#603000; ">QMessageBox</span><span style="color:#800080; ">::</span>warning<span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">warning</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">cannot open file:</span><span style="color:#800000; ">"</span><span style="color:#808030; ">+</span>file<span style="color:#808030; ">.</span>errorString<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
          <span style="color:#800000; font-weight:bold; ">return</span><span style="color:#800080; ">;</span>
    <span style="color:#800080; ">}</span>
    setWindowTitle<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
       <span style="color:#603000; ">QTextStream</span> in<span style="color:#808030; ">(</span><span style="color:#808030; ">&amp;</span>file<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
       <span style="color:#603000; ">QString</span> text<span style="color:#808030; ">=</span>in<span style="color:#808030; ">.</span>readAll<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
       ui<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>textEdit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setPlainText<span style="color:#808030; ">(</span>text<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
       file<span style="color:#808030; ">.</span>close<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>

<span style="color:#800000; font-weight:bold; ">void</span> TextEditor<span style="color:#800080; ">::</span>on_actionsave_triggered<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span><span style="color:#808030; ">!</span>currentFile<span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
    <span style="color:#800000; font-weight:bold; ">auto</span> filename<span style="color:#808030; ">=</span> <span style="color:#603000; ">QFileDialog</span><span style="color:#800080; ">::</span>getSaveFileName<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QFile</span> file<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    currentFile<span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QString</span><span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span><span style="color:#808030; ">!</span>file<span style="color:#808030; ">.</span>open<span style="color:#808030; ">(</span><span style="color:#603000; ">QFile</span><span style="color:#800080; ">::</span>WriteOnly <span style="color:#808030; ">|</span> <span style="color:#603000; ">QFile</span><span style="color:#800080; ">::</span>Text<span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
        <span style="color:#603000; ">QMessageBox</span><span style="color:#800080; ">::</span>warning<span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">warning</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">cannot save file:</span><span style="color:#800000; ">"</span><span style="color:#808030; ">+</span>file<span style="color:#808030; ">.</span>errorString<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
        <span style="color:#800000; font-weight:bold; ">return</span><span style="color:#800080; ">;</span>
    <span style="color:#800080; ">}</span>
    setWindowTitle<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QTextStream</span> out<span style="color:#808030; ">(</span><span style="color:#808030; ">&amp;</span>file<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QString</span> text<span style="color:#808030; ">=</span>ui<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>textEdit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>toPlainText<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    out <span style="color:#808030; ">&lt;</span><span style="color:#808030; ">&lt;</span>text<span style="color:#800080; ">;</span>
    file<span style="color:#808030; ">.</span>close<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#800080; ">}</span>
<span style="color:#800080; ">}</span>


<span style="color:#800000; font-weight:bold; ">void</span> TextEditor<span style="color:#800080; ">::</span>on_actionsave_as_triggered<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span>currentFile<span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
    <span style="color:#800000; font-weight:bold; ">auto</span> filename<span style="color:#808030; ">=</span> <span style="color:#603000; ">QFileDialog</span><span style="color:#800080; ">::</span>getSaveFileName<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QFile</span> file<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
currentFile<span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QString</span><span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span><span style="color:#808030; ">!</span>file<span style="color:#808030; ">.</span>open<span style="color:#808030; ">(</span><span style="color:#603000; ">QFile</span><span style="color:#800080; ">::</span>WriteOnly <span style="color:#808030; ">|</span> <span style="color:#603000; ">QFile</span><span style="color:#800080; ">::</span>Text<span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
        <span style="color:#603000; ">QMessageBox</span><span style="color:#800080; ">::</span>warning<span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">warning</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">cannot save file:</span><span style="color:#800000; ">"</span><span style="color:#808030; ">+</span>file<span style="color:#808030; ">.</span>errorString<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
        <span style="color:#800000; font-weight:bold; ">return</span><span style="color:#800080; ">;</span>
    <span style="color:#800080; ">}</span>
    setWindowTitle<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QTextStream</span> out<span style="color:#808030; ">(</span><span style="color:#808030; ">&amp;</span>file<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QString</span> text<span style="color:#808030; ">=</span>ui<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>textEdit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>toPlainText<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    out <span style="color:#808030; ">&lt;</span><span style="color:#808030; ">&lt;</span>text<span style="color:#800080; ">;</span>
    file<span style="color:#808030; ">.</span>close<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#800080; ">}</span>
<span style="color:#800080; ">}</span>


<span style="color:#800000; font-weight:bold; ">void</span> TextEditor<span style="color:#800080; ">::</span>on_actionexit_triggered<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    <span style="color:#603000; ">QApplication</span><span style="color:#800080; ">::</span>quit<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>


<span style="color:#800000; font-weight:bold; ">void</span> TextEditor<span style="color:#800080; ">::</span>on_actioncut_triggered<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    ui<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>textEdit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>cut<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>


<span style="color:#800000; font-weight:bold; ">void</span> TextEditor<span style="color:#800080; ">::</span>on_actioncopy_triggered<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    ui<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>textEdit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span><span style="color:#603000; ">copy</span><span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>


<span style="color:#800000; font-weight:bold; ">void</span> TextEditor<span style="color:#800080; ">::</span>on_actionpaste_triggered<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    ui<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>textEdit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>paste<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>


<span style="color:#800000; font-weight:bold; ">void</span> TextEditor<span style="color:#800080; ">::</span>on_actionabout_triggered<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    <span style="color:#603000; ">QMessageBox</span><span style="color:#800080; ">::</span>about<span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">about me</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">this is my text editor</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>


<span style="color:#800000; font-weight:bold; ">void</span> TextEditor<span style="color:#800080; ">::</span>on_actionabout_qt_triggered<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
     <span style="color:#603000; ">QMessageBox</span><span style="color:#800080; ">::</span>aboutQt<span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">about Qt</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>
</pre>

# textEdit.cpp

<pre style="color:#000000;background:#ffffff;"><span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">"</span><span style="color:#40015a; ">texteditor.h</span><span style="color:#800000; ">"</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include </span><span style="color:#800000; ">"</span><span style="color:#40015a; ">ui_texteditor.h</span><span style="color:#800000; ">"</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include</span><span style="color:#800000; ">"</span><span style="color:#40015a; ">QFileDialog</span><span style="color:#800000; ">"</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include</span><span style="color:#800000; ">"</span><span style="color:#40015a; ">QMessageBox</span><span style="color:#800000; ">"</span>
<span style="color:#004a43; ">#</span><span style="color:#004a43; ">include</span><span style="color:#800000; ">"</span><span style="color:#40015a; ">QTextStream</span><span style="color:#800000; ">"</span>

TextEditor<span style="color:#800080; ">::</span>TextEditor<span style="color:#808030; ">(</span><span style="color:#603000; ">QWidget</span> <span style="color:#808030; ">*</span>parent<span style="color:#808030; ">)</span> <span style="color:#800080; ">:</span>
    <span style="color:#603000; ">QDialog</span><span style="color:#808030; ">(</span>parent<span style="color:#808030; ">)</span><span style="color:#808030; ">,</span>
    ui<span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">new</span> Ui<span style="color:#800080; ">::</span>TextEditor<span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    ui<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setupUi<span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#696969; ">//setCentralWidget(ui-&gt;textEdit);</span>
    currentFile<span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">nullptr</span><span style="color:#800080; ">;</span>
    setWindowTitle<span style="color:#808030; ">(</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">buffer</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>

TextEditor<span style="color:#800080; ">::</span><span style="color:#808030; ">~</span>TextEditor<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    <span style="color:#800000; font-weight:bold; ">delete</span> ui<span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>

<span style="color:#800000; font-weight:bold; ">void</span> TextEditor<span style="color:#800080; ">::</span>on_actionnew_triggered<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
<span style="color:#696969; ">//    currentFile-&gt;clear();</span>
    ui<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>textEdit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setPlainText<span style="color:#808030; ">(</span><span style="color:#603000; ">QString</span><span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>


<span style="color:#800000; font-weight:bold; ">void</span> TextEditor<span style="color:#800080; ">::</span>on_actionopen_triggered<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    <span style="color:#800000; font-weight:bold; ">auto</span> filename<span style="color:#808030; ">=</span> <span style="color:#603000; ">QFileDialog</span><span style="color:#800080; ">::</span>getOpenFileName<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QFile</span> file<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    currentFile<span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QString</span><span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span><span style="color:#808030; ">!</span>file<span style="color:#808030; ">.</span>open<span style="color:#808030; ">(</span><span style="color:#603000; ">QIODevice</span><span style="color:#800080; ">::</span>ReadOnly <span style="color:#808030; ">|</span> <span style="color:#603000; ">QFile</span><span style="color:#800080; ">::</span>Text<span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
        <span style="color:#603000; ">QMessageBox</span><span style="color:#800080; ">::</span>warning<span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">warning</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">cannot open file:</span><span style="color:#800000; ">"</span><span style="color:#808030; ">+</span>file<span style="color:#808030; ">.</span>errorString<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
          <span style="color:#800000; font-weight:bold; ">return</span><span style="color:#800080; ">;</span>
    <span style="color:#800080; ">}</span>
    setWindowTitle<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
       <span style="color:#603000; ">QTextStream</span> in<span style="color:#808030; ">(</span><span style="color:#808030; ">&amp;</span>file<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
       <span style="color:#603000; ">QString</span> text<span style="color:#808030; ">=</span>in<span style="color:#808030; ">.</span>readAll<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
       ui<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>textEdit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>setPlainText<span style="color:#808030; ">(</span>text<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
       file<span style="color:#808030; ">.</span>close<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>

<span style="color:#800000; font-weight:bold; ">void</span> TextEditor<span style="color:#800080; ">::</span>on_actionsave_triggered<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span><span style="color:#808030; ">!</span>currentFile<span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
    <span style="color:#800000; font-weight:bold; ">auto</span> filename<span style="color:#808030; ">=</span> <span style="color:#603000; ">QFileDialog</span><span style="color:#800080; ">::</span>getSaveFileName<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QFile</span> file<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    currentFile<span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QString</span><span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span><span style="color:#808030; ">!</span>file<span style="color:#808030; ">.</span>open<span style="color:#808030; ">(</span><span style="color:#603000; ">QFile</span><span style="color:#800080; ">::</span>WriteOnly <span style="color:#808030; ">|</span> <span style="color:#603000; ">QFile</span><span style="color:#800080; ">::</span>Text<span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
        <span style="color:#603000; ">QMessageBox</span><span style="color:#800080; ">::</span>warning<span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">warning</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">cannot save file:</span><span style="color:#800000; ">"</span><span style="color:#808030; ">+</span>file<span style="color:#808030; ">.</span>errorString<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
        <span style="color:#800000; font-weight:bold; ">return</span><span style="color:#800080; ">;</span>
    <span style="color:#800080; ">}</span>
    setWindowTitle<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QTextStream</span> out<span style="color:#808030; ">(</span><span style="color:#808030; ">&amp;</span>file<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QString</span> text<span style="color:#808030; ">=</span>ui<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>textEdit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>toPlainText<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    out <span style="color:#808030; ">&lt;</span><span style="color:#808030; ">&lt;</span>text<span style="color:#800080; ">;</span>
    file<span style="color:#808030; ">.</span>close<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#800080; ">}</span>
<span style="color:#800080; ">}</span>


<span style="color:#800000; font-weight:bold; ">void</span> TextEditor<span style="color:#800080; ">::</span>on_actionsave_as_triggered<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span>currentFile<span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
    <span style="color:#800000; font-weight:bold; ">auto</span> filename<span style="color:#808030; ">=</span> <span style="color:#603000; ">QFileDialog</span><span style="color:#800080; ">::</span>getSaveFileName<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QFile</span> file<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
currentFile<span style="color:#808030; ">=</span><span style="color:#800000; font-weight:bold; ">new</span> <span style="color:#603000; ">QString</span><span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#800000; font-weight:bold; ">if</span><span style="color:#808030; ">(</span><span style="color:#808030; ">!</span>file<span style="color:#808030; ">.</span>open<span style="color:#808030; ">(</span><span style="color:#603000; ">QFile</span><span style="color:#800080; ">::</span>WriteOnly <span style="color:#808030; ">|</span> <span style="color:#603000; ">QFile</span><span style="color:#800080; ">::</span>Text<span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">{</span>
        <span style="color:#603000; ">QMessageBox</span><span style="color:#800080; ">::</span>warning<span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">warning</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">cannot save file:</span><span style="color:#800000; ">"</span><span style="color:#808030; ">+</span>file<span style="color:#808030; ">.</span>errorString<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
        <span style="color:#800000; font-weight:bold; ">return</span><span style="color:#800080; ">;</span>
    <span style="color:#800080; ">}</span>
    setWindowTitle<span style="color:#808030; ">(</span>filename<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QTextStream</span> out<span style="color:#808030; ">(</span><span style="color:#808030; ">&amp;</span>file<span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#603000; ">QString</span> text<span style="color:#808030; ">=</span>ui<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>textEdit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>toPlainText<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    out <span style="color:#808030; ">&lt;</span><span style="color:#808030; ">&lt;</span>text<span style="color:#800080; ">;</span>
    file<span style="color:#808030; ">.</span>close<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
    <span style="color:#800080; ">}</span>
<span style="color:#800080; ">}</span>


<span style="color:#800000; font-weight:bold; ">void</span> TextEditor<span style="color:#800080; ">::</span>on_actionexit_triggered<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    <span style="color:#603000; ">QApplication</span><span style="color:#800080; ">::</span>quit<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>


<span style="color:#800000; font-weight:bold; ">void</span> TextEditor<span style="color:#800080; ">::</span>on_actioncut_triggered<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    ui<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>textEdit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>cut<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>


<span style="color:#800000; font-weight:bold; ">void</span> TextEditor<span style="color:#800080; ">::</span>on_actioncopy_triggered<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    ui<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>textEdit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span><span style="color:#603000; ">copy</span><span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>


<span style="color:#800000; font-weight:bold; ">void</span> TextEditor<span style="color:#800080; ">::</span>on_actionpaste_triggered<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    ui<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>textEdit<span style="color:#808030; ">-</span><span style="color:#808030; ">&gt;</span>paste<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>


<span style="color:#800000; font-weight:bold; ">void</span> TextEditor<span style="color:#800080; ">::</span>on_actionabout_triggered<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
    <span style="color:#603000; ">QMessageBox</span><span style="color:#800080; ">::</span>about<span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">about me</span><span style="color:#800000; ">"</span><span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">this is my text editor</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>


<span style="color:#800000; font-weight:bold; ">void</span> TextEditor<span style="color:#800080; ">::</span>on_actionabout_qt_triggered<span style="color:#808030; ">(</span><span style="color:#808030; ">)</span>
<span style="color:#800080; ">{</span>
     <span style="color:#603000; ">QMessageBox</span><span style="color:#800080; ">::</span>aboutQt<span style="color:#808030; ">(</span><span style="color:#800000; font-weight:bold; ">this</span><span style="color:#808030; ">,</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">about Qt</span><span style="color:#800000; ">"</span><span style="color:#808030; ">)</span><span style="color:#800080; ">;</span>
<span style="color:#800080; ">}</span>
</pre>

![WhatsApp Image 2021-12-19 at 17 55 53](https://user-images.githubusercontent.com/86810485/146685964-265033f4-ae54-456b-aa24-b4be30730b5a.jpeg)


  # Icons
Add a resource file and add a set of predefined icons from you choice.

# icons.cpp

<pre style="color:#000000;background:#ffffff;"><span style="color:#808030; ">&lt;</span>RCC<span style="color:#808030; ">&gt;</span>
    <span style="color:#808030; ">&lt;</span>qresource prefix<span style="color:#808030; ">=</span><span style="color:#800000; ">"</span><span style="color:#0000e6; ">/</span><span style="color:#800000; ">"</span><span style="color:#808030; ">&gt;</span>
        <span style="color:#800080; ">&lt;</span>file<span style="color:#800080; ">&gt;</span>icons<span style="color:#808030; ">/</span>cut_icon<span style="color:#808030; ">.</span>png<span style="color:#808030; ">&lt;</span><span style="color:#808030; ">/</span>file<span style="color:#808030; ">&gt;</span>
        <span style="color:#800080; ">&lt;</span>file<span style="color:#800080; ">&gt;</span>icons<span style="color:#808030; ">/</span>new_file<span style="color:#808030; ">.</span>png<span style="color:#808030; ">&lt;</span><span style="color:#808030; ">/</span>file<span style="color:#808030; ">&gt;</span>
        <span style="color:#800080; ">&lt;</span>file<span style="color:#800080; ">&gt;</span>icons<span style="color:#808030; ">/</span>quit_icon<span style="color:#808030; ">.</span>png<span style="color:#808030; ">&lt;</span><span style="color:#808030; ">/</span>file<span style="color:#808030; ">&gt;</span>
        <span style="color:#800080; ">&lt;</span>file<span style="color:#800080; ">&gt;</span>icons<span style="color:#808030; ">/</span>save_file<span style="color:#808030; ">.</span>png<span style="color:#808030; ">&lt;</span><span style="color:#808030; ">/</span>file<span style="color:#808030; ">&gt;</span>
        <span style="color:#800080; ">&lt;</span>file<span style="color:#800080; ">&gt;</span>icons<span style="color:#808030; ">/</span>search_icon<span style="color:#808030; ">.</span>png<span style="color:#808030; ">&lt;</span><span style="color:#808030; ">/</span>file<span style="color:#808030; ">&gt;</span>
    <span style="color:#808030; ">&lt;</span><span style="color:#808030; ">/</span>qresource<span style="color:#808030; ">&gt;</span>
<span style="color:#808030; ">&lt;</span><span style="color:#808030; ">/</span>RCC<span style="color:#808030; ">&gt;</span>
</pre>

 * search-icon
 ![search_icon](https://user-images.githubusercontent.com/86810485/146686807-e0bcb267-4dea-4371-a97b-4ca5acc76817.png)

  * save-file
  ![save_file](https://user-images.githubusercontent.com/86810485/146686822-b9cd5fe0-a6e0-4496-9290-1fbae182e2d8.png)

  * quit-icon
  ![quit_icon](https://user-images.githubusercontent.com/86810485/146686834-8435277c-e5af-4c87-81bb-02d575687fa2.png)

  * new-file
  ![new_file](https://user-images.githubusercontent.com/86810485/146686845-7518a01f-9fcd-48a1-822f-6f090dd14707.png)

  *cut-icon
  ![cut_icon](https://user-images.githubusercontent.com/86810485/146686860-a04be7ab-8b69-4c62-960d-e46cd2d3add2.png)


  # Menus
Using the designer add all the menues in the image description.
      
  # All the actions that we added are included in the menu as it is shown previously.

  # Functionality
Now using your knowledge, try to code the functionality of the Text Editor. Here is a link for QPlainTextEdit.

  # see above
