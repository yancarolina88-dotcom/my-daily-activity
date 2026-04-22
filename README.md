
<!DOCTYPE html>
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>Daily Planner</title>
  
  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  
  <!-- React & ReactDOM -->
  <script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
  
  <!-- Babel for JSX -->
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
  
  <!-- Lucide Icons -->
  <script src="https://unpkg.com/lucide@latest"></script>

  <style>
    @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;500;600;700&display=swap');
    body {
      font-family: 'Outfit', sans-serif;
      background-color: #FDFBF7;
    }
    .custom-scrollbar::-webkit-scrollbar { height: 6px; width: 6px; }
    .custom-scrollbar::-webkit-scrollbar-track { background: #F8F5F0; border-radius: 10px; }
    .custom-scrollbar::-webkit-scrollbar-thumb { background: #D3C1A1; border-radius: 10px; }
    
    /* Utility classes for Lucide icons */
    .icon { width: 24px; height: 24px; stroke-width: 2; stroke: currentColor; fill: none; stroke-linecap: round; stroke-linejoin: round; }
  </style>
</head>
<body>
  <div id="root"></div>

  <script type="text/babel">
    const { useState, useEffect } = React;

    // Helper component to render Lucide SVG icons in standalone React
    const Icon = ({ name, size = 24, className = '', strokeWidth = 2 }) => {
      const svgString = lucide.icons[name].toSvg({ width: size, height: size, 'stroke-width': strokeWidth });
      return <span className={`inline-flex items-center justify-center ${className}`} dangerouslySetInnerHTML={{ __html: svgString }} />;
    };

    function App() {
      const [activeTab, setActiveTab] = useState('daily');
      const [currentDate, setCurrentDate] = useState(new Date());

      // Data States
      const [dailyTasks, setDailyTasks] = useState(() => JSON.parse(localStorage.getItem('app_tasks')) || []);
      const [dailyVerse, setDailyVerse] = useState(() => JSON.parse(localStorage.getItem('app_verse')) || { reference: '', reflection: '' });
      const [monthlyGoals, setMonthlyGoals] = useState(() => JSON.parse(localStorage.getItem('app_goals')) || []);
      const [journals, setJournals] = useState(() => JSON.parse(localStorage.getItem('app_journals')) || []);
      const [transactions, setTransactions] = useState(() => JSON.parse(localStorage.getItem('app_finances_twd')) || []);

      // Save Data Effects
      useEffect(() => localStorage.setItem('app_tasks', JSON.stringify(dailyTasks)), [dailyTasks]);
      useEffect(() => localStorage.setItem('app_verse', JSON.stringify(dailyVerse)), [dailyVerse]);
      useEffect(() => localStorage.setItem('app_goals', JSON.stringify(monthlyGoals)), [monthlyGoals]);
      useEffect(() => localStorage.setItem('app_journals', JSON.stringify(journals)), [journals]);
      useEffect(() => localStorage.setItem('app_finances_twd', JSON.stringify(transactions)), [transactions]);

      const formatDate = (date) => new Intl.DateTimeFormat('en-US', { weekday: 'long', day: 'numeric', month: 'long', year: 'numeric' }).format(date);
      const currentMonthYear = new Intl.DateTimeFormat('en-US', { month: 'long', year: 'numeric' }).format(currentDate);

      return (
        <div className="min-h-screen text-stone-800 flex justify-center">
          <div className="w-full max-w-md bg-[#FDFBF7] shadow-sm min-h-screen relative flex flex-col pb-20">
            
            <header className="pt-10 pb-4 px-6 bg-white/80 backdrop-blur-md sticky top-0 z-10 border-b border-stone-100">
              <h1 className="text-3xl font-semibold text-stone-700 tracking-tight">
                {activeTab === 'daily' && 'Hello! 👋'}
                {activeTab === 'monthly' && 'Monthly Goals'}
                {activeTab === 'journal' && 'My Journal'}
                {activeTab === 'finance' && 'My Finances'}
                {activeTab === 'summary' && 'Monthly Summary'}
              </h1>
              <p className="text-sm text-stone-500 mt-1 font-medium">
                {activeTab === 'summary' ? currentMonthYear : formatDate(currentDate)}
              </p>
            </header>

            <main className="flex-1 p-6 overflow-y-auto">
              {activeTab === 'daily' && <DailyView tasks={dailyTasks} setTasks={setDailyTasks} verse={dailyVerse} setVerse={setDailyVerse} />}
              {activeTab === 'monthly' && <MonthlyView goals={monthlyGoals} setGoals={setMonthlyGoals} currentMonthYear={currentMonthYear} />}
              {activeTab === 'journal' && <JournalView journals={journals} setJournals={setJournals} />}
              {activeTab === 'finance' && <FinanceView transactions={transactions} setTransactions={setTransactions} currentMonthYear={currentMonthYear} />}
              {activeTab === 'summary' && <SummaryView tasks={dailyTasks} goals={monthlyGoals} journals={journals} transactions={transactions} currentDate={currentDate} />}
            </main>

            <nav className="fixed bottom-0 w-full max-w-md bg-white border-t border-stone-100 flex justify-between px-1 py-3 z-20" style={{ paddingBottom: 'env(safe-area-inset-bottom, 12px)' }}>
              <NavItem icon="CheckSquare" label="Daily" isActive={activeTab === 'daily'} onClick={() => setActiveTab('daily')} />
              <NavItem icon="CalendarDays" label="Monthly" isActive={activeTab === 'monthly'} onClick={() => setActiveTab('monthly')} />
              <NavItem icon="BookOpen" label="Journal" isActive={activeTab === 'journal'} onClick={() => setActiveTab('journal')} />
              <NavItem icon="Wallet" label="Finance" isActive={activeTab === 'finance'} onClick={() => setActiveTab('finance')} />
              <NavItem icon="LayoutList" label="Summary" isActive={activeTab === 'summary'} onClick={() => setActiveTab('summary')} />
            </nav>
          </div>
        </div>
      );
    }

    function NavItem({ icon, label, isActive, onClick }) {
      return (
        <button onClick={onClick} className={`flex flex-col items-center justify-center flex-1 gap-1.5 transition-all duration-300 ${isActive ? 'text-[#8AA6A3]' : 'text-stone-300 hover:text-stone-500'}`}>
          <div className={`${isActive ? 'scale-110' : 'scale-100'} transition-transform duration-300`}>
            <Icon name={icon} size={20} strokeWidth={2.5} />
          </div>
          <span className="text-[9px] sm:text-[10px] font-semibold tracking-wide">{label}</span>
        </button>
      );
    }

    // --- DAILY VIEW ---
    function DailyView({ tasks, setTasks, verse, setVerse }) {
      const [newTask, setNewTask] = useState('');
      const [isEditingVerse, setIsEditingVerse] = useState(false);
      const [tempVerseRef, setTempVerseRef] = useState(verse.reference);
      const [tempReflection, setTempReflection] = useState(verse.reflection);

      const addTask = (e) => {
        e.preventDefault();
        if (!newTask.trim()) return;
        setTasks([...tasks, { id: Date.now(), text: newTask, completed: false, date: new Date().toISOString() }]);
        setNewTask('');
      };

      const toggleTask = (id) => setTasks(tasks.map(t => t.id === id ? { ...t, completed: !t.completed } : t));
      const deleteTask = (id) => setTasks(tasks.filter(t => t.id !== id));

      const saveVerse = () => {
        setVerse({ reference: tempVerseRef, reflection: tempReflection });
        setIsEditingVerse(false);
      };

      const completedCount = tasks.filter(t => t.completed).length;
      const progress = tasks.length === 0 ? 0 : Math.round((completedCount / tasks.length) * 100);
      const isAllDone = tasks.length > 0 && completedCount === tasks.length;

      return (
        <div className="space-y-7 animate-in fade-in duration-500">
          {/* Verse Card */}
          <div className="bg-[#F8F5F0] rounded-2xl p-5 border border-stone-200 shadow-sm relative overflow-hidden">
            <div className="absolute -right-4 -top-4 opacity-5 text-[#D3C1A1]"><Icon name="BookHeart" size={100} /></div>
            <div className="flex items-center justify-between mb-3 relative z-10">
              <div className="flex items-center gap-2 text-[#D3C1A1]">
                <Icon name="BookHeart" size={18} />
                <h2 className="text-sm font-semibold text-stone-600 tracking-wide uppercase">Today's Devotion</h2>
              </div>
              {!isEditingVerse && (
                <button onClick={() => { setTempVerseRef(verse.reference); setTempReflection(verse.reflection); setIsEditingVerse(true); }} className="text-stone-400 hover:text-[#D3C1A1]">
                  <Icon name="PenTool" size={14} />
                </button>
              )}
            </div>
            {isEditingVerse ? (
              <div className="space-y-3 relative z-10">
                <input type="text" value={tempVerseRef} onChange={(e) => setTempVerseRef(e.target.value)} placeholder="Verse Reference" className="w-full px-3 py-2 text-sm rounded-lg border border-stone-200" />
                <textarea value={tempReflection} onChange={(e) => setTempReflection(e.target.value)} placeholder="What did you learn?" className="w-full px-3 py-2 text-sm rounded-lg border border-stone-200 h-20 resize-none" />
                <button onClick={saveVerse} className="w-full bg-[#D3C1A1] text-white py-2 rounded-lg text-sm font-medium">Save Reflection</button>
              </div>
            ) : (
              <div className="relative z-10">
                {verse.reflection || verse.reference ? (
                  <>
                    {verse.reference && <p className="text-xs font-bold text-[#D3C1A1] mb-1.5 uppercase">{verse.reference}</p>}
                    {verse.reflection && <p className="text-stone-700 text-sm font-medium">{verse.reflection}</p>}
                  </>
                ) : (
                  <div className="cursor-pointer py-3 border-2 border-dashed border-[#D3C1A1]/30 rounded-xl text-center" onClick={() => setIsEditingVerse(true)}>
                    <p className="text-sm text-[#D3C1A1] font-medium">+ Add today's verse & reflection</p>
                  </div>
                )}
              </div>
            )}
          </div>

          {/* Progress Card */}
          <div className={`p-5 rounded-2xl shadow-sm border ${isAllDone ? 'bg-gradient-to-br from-[#FFF8E7] to-[#FCEABB] border-[#F4D06F]' : 'bg-gradient-to-br from-[#E8F0EF] to-[#D5E3E1] border-white/50'}`}>
            {isAllDone ? (
              <div className="flex flex-col items-center text-center">
                <div className="bg-white p-3 rounded-full shadow-sm mb-3"><Icon name="Trophy" size={28} className="text-[#F2B705]" /></div>
                <h2 className="text-[#B08500] font-bold mb-1 text-lg">Amazing Job! 🎉</h2>
                <p className="text-xs text-[#9E7700] font-medium">You have completed all your tasks for today.</p>
              </div>
            ) : (
              <>
                <h2 className="text-stone-700 font-semibold mb-3">Daily Progress</h2>
                <div className="w-full bg-white/60 rounded-full h-3 mb-2 overflow-hidden">
                  <div className="bg-[#8AA6A3] h-3 rounded-full transition-all duration-700" style={{ width: `${progress}%` }}></div>
                </div>
                <p className="text-xs text-stone-600 font-medium">{completedCount} of {tasks.length} completed ({progress}%)</p>
              </>
            )}
          </div>

          {/* Tasks List */}
          <div>
            <div className="flex items-center gap-2 mb-4 text-[#8AA6A3]">
              <Icon name="CheckSquare" size={18} />
              <h2 className="text-sm font-semibold text-stone-600 uppercase">To-Do List</h2>
            </div>
            <form onSubmit={addTask} className="flex gap-2 mb-4">
              <input type="text" value={newTask} onChange={(e) => setNewTask(e.target.value)} placeholder="What needs to be done?" className="flex-1 px-4 py-3 rounded-xl border border-stone-200 text-sm" />
              <button type="submit" className="bg-[#8AA6A3] text-white px-4 rounded-xl flex items-center"><Icon name="Plus" size={22} /></button>
            </form>
            <div className="space-y-2.5">
              {tasks.length === 0 ? (
                <div className="text-center py-8 text-stone-400 text-sm bg-white/50 rounded-xl border border-dashed border-stone-200">No tasks yet. Enjoy your day!</div>
              ) : (
                tasks.map(task => (
                  <div key={task.id} className={`flex items-center justify-between p-4 rounded-xl border ${task.completed ? 'bg-stone-50 opacity-60' : 'bg-white shadow-sm'}`}>
                    <div className="flex items-center gap-3 flex-1 cursor-pointer" onClick={() => toggleTask(task.id)}>
                      {task.completed ? <Icon name="CheckCircle2" size={22} className="text-[#8AA6A3]" /> : <Icon name="Circle" size={22} className="text-stone-300" />}
                      <span className={`text-sm font-medium ${task.completed ? 'line-through text-stone-400' : 'text-stone-700'}`}>{task.text}</span>
                    </div>
                    <button onClick={() => deleteTask(task.id)} className="text-stone-300 hover:text-red-400 p-1"><Icon name="Trash2" size={16} /></button>
                  </div>
                ))
              )}
            </div>
          </div>
        </div>
      );
    }

    // --- MONTHLY VIEW ---
    function MonthlyView({ goals, setGoals, currentMonthYear }) {
      const [newGoal, setNewGoal] = useState('');
      const addGoal = (e) => { e.preventDefault(); if (!newGoal.trim()) return; setGoals([...goals, { id: Date.now(), text: newGoal, completed: false, date: new Date().toISOString() }]); setNewGoal(''); };
      const toggleGoal = (id) => setGoals(goals.map(g => g.id === id ? { ...g, completed: !g.completed } : g));
      const deleteGoal = (id) => setGoals(goals.filter(g => g.id !== id));

      return (
        <div className="space-y-7">
          <div className="bg-gradient-to-br from-[#FDF0F0] to-[#F8DDDD] p-6 rounded-2xl shadow-sm text-center">
            <p className="text-xs text-[#D49A9A] uppercase tracking-[0.2em] font-bold mb-1">Focus For</p>
            <h2 className="text-2xl font-bold text-rose-900">{currentMonthYear}</h2>
          </div>
          <div>
            <form onSubmit={addGoal} className="flex gap-2 mb-4">
              <input type="text" value={newGoal} onChange={(e) => setNewGoal(e.target.value)} placeholder="Add a new goal..." className="flex-1 px-4 py-3 rounded-xl border border-stone-200 text-sm" />
              <button type="submit" className="bg-[#D49A9A] text-white px-4 rounded-xl flex items-center"><Icon name="Plus" size={22} /></button>
            </form>
            <div className="space-y-2.5">
              {goals.length === 0 ? (
                <div className="text-center py-12 text-stone-400 text-sm bg-white/50 rounded-xl border border-dashed"><Icon name="CalendarDays" size={32} className="mx-auto mb-3 opacity-30 text-[#D49A9A]" /><p>What do you want to achieve?</p></div>
              ) : (
                goals.map(g => (
                  <div key={g.id} className={`flex items-center p-4 rounded-xl border ${g.completed ? 'bg-rose-50/40 opacity-60' : 'bg-white shadow-sm'}`}>
                    <div className="flex items-center gap-3 flex-1 cursor-pointer" onClick={() => toggleGoal(g.id)}>
                      {g.completed ? <Icon name="CheckCircle2" size={22} className="text-[#D49A9A]" /> : <Icon name="Circle" size={22} className="text-stone-300" />}
                      <span className={`text-sm font-medium ${g.completed ? 'line-through text-stone-400' : 'text-stone-700'}`}>{g.text}</span>
                    </div>
                    <button onClick={() => deleteGoal(g.id)} className="text-stone-300 hover:text-red-400 p-1"><Icon name="Trash2" size={16} /></button>
                  </div>
                ))
              )}
            </div>
          </div>
        </div>
      );
    }

    // --- JOURNAL VIEW ---
    function JournalView({ journals, setJournals }) {
      const [text, setText] = useState('');
      const save = () => { if (!text.trim()) return; setJournals([{ id: Date.now(), text, date: new Date().toISOString() }, ...journals]); setText(''); };

      return (
        <div className="space-y-7">
          <div className="bg-white p-5 rounded-2xl shadow-sm border border-stone-200">
            <textarea value={text} onChange={(e) => setText(e.target.value)} placeholder="Write your thoughts here..." className="w-full h-32 resize-none bg-transparent focus:outline-none text-sm"></textarea>
            <div className="flex justify-between items-center mt-2 pt-3 border-t border-stone-100">
              <span className="text-xs text-stone-400">{new Intl.DateTimeFormat('en-US', { dateStyle: 'medium' }).format(new Date())}</span>
              <button onClick={save} className="bg-stone-800 text-white px-5 py-2 rounded-xl text-sm font-semibold">Save Entry</button>
            </div>
          </div>
          <div className="space-y-4">
            <h3 className="text-sm font-bold text-stone-500 pl-1 uppercase">Past Entries</h3>
            {journals.map(j => (
              <div key={j.id} className="bg-white p-5 rounded-2xl shadow-sm border border-stone-100 flex gap-4">
                <div className="flex flex-col items-center min-w-[50px] border-r border-stone-100 pr-4">
                  <span className="text-2xl font-bold text-stone-700">{new Date(j.date).getDate()}</span>
                  <span className="text-xs font-bold text-[#D3C1A1] uppercase">{new Intl.DateTimeFormat('en-US', { month: 'short' }).format(new Date(j.date))}</span>
                </div>
                <div className="flex-1">
                  <div className="flex justify-between items-start mb-2">
                    <span className="text-[10px] font-semibold text-stone-400 bg-stone-100 px-2 py-1 rounded-full">{new Intl.DateTimeFormat('en-US', { hour: 'numeric', minute:'2-digit' }).format(new Date(j.date))}</span>
                    <button onClick={() => setJournals(journals.filter(x => x.id !== j.id))} className="text-stone-300 hover:text-red-400"><Icon name="Trash2" size={16} /></button>
                  </div>
                  <p className="text-sm font-medium text-stone-600 whitespace-pre-wrap">{j.text}</p>
                </div>
              </div>
            ))}
          </div>
        </div>
      );
    }

    // --- FINANCE VIEW ---
    function FinanceView({ transactions, setTransactions, currentMonthYear }) {
      const [desc, setDesc] = useState('');
      const [amount, setAmount] = useState('');
      const [type, setType] = useState('expense');
      const [rate, setRate] = useState(null);

      useEffect(() => { fetch('https://open.er-api.com/v6/latest/TWD').then(r => r.json()).then(d => setRate(d.rates?.IDR)).catch(e => console.log(e)); }, []);

      const add = (e) => { e.preventDefault(); if (!desc || !amount) return; setTransactions([{ id: Date.now(), description: desc, amount: parseFloat(amount), type, date: new Date().toISOString() }, ...transactions]); setDesc(''); setAmount(''); };

      const current = transactions.filter(t => new Date(t.date).getMonth() === new Date().getMonth() && new Date(t.date).getFullYear() === new Date().getFullYear());
      const income = current.filter(t => t.type === 'income').reduce((s, t) => s + t.amount, 0);
      const expense = current.filter(t => t.type === 'expense').reduce((s, t) => s + t.amount, 0);
      const balance = income - expense;

      return (
        <div className="space-y-6">
          <div className="bg-gradient-to-br from-[#EBF4F6] to-[#D6E6E8] p-6 rounded-2xl shadow-sm text-center">
            <p className="text-xs text-[#5C838A] uppercase font-bold mb-1">{currentMonthYear} Balance</p>
            <h2 className={`text-3xl font-bold mb-4 ${balance >= 0 ? 'text-[#3E6166]' : 'text-rose-700'}`}>NT$ {balance.toLocaleString()}</h2>
            <div className="flex justify-between bg-white/50 rounded-xl p-3">
              <div className="flex flex-col items-center flex-1"><span className="text-[10px] text-emerald-600 font-bold uppercase mb-1">Income</span><span className="text-sm font-semibold">NT$ {income.toLocaleString()}</span></div>
              <div className="flex flex-col items-center flex-1 border-l border-stone-300/50"><span className="text-[10px] text-rose-500 font-bold uppercase mb-1">Expense</span><span className="text-sm font-semibold">NT$ {expense.toLocaleString()}</span></div>
            </div>
          </div>

          <div className="bg-stone-50 border border-stone-200 rounded-xl p-3 flex justify-between items-center text-xs font-semibold text-stone-600">
            <span>Live Rate (TWD to IDR)</span><span className="bg-white px-2 py-1 rounded">{rate ? `1 NT$ = Rp ${rate.toLocaleString('id-ID', {maximumFractionDigits:0})}` : 'Loading...'}</span>
          </div>

          <div className="bg-white p-4 rounded-2xl shadow-sm border border-stone-200">
            <div className="flex gap-2 mb-3">
              <button onClick={() => setType('expense')} className={`flex-1 py-2 rounded-lg text-xs font-bold uppercase ${type === 'expense' ? 'bg-rose-100 text-rose-700' : 'bg-stone-100'}`}>Expense</button>
              <button onClick={() => setType('income')} className={`flex-1 py-2 rounded-lg text-xs font-bold uppercase ${type === 'income' ? 'bg-emerald-100 text-emerald-700' : 'bg-stone-100'}`}>Income</button>
            </div>
            <form onSubmit={add} className="flex gap-2 flex-wrap">
              <input type="text" value={desc} onChange={e => setDesc(e.target.value)} placeholder="Description" className="flex-1 min-w-[120px] px-3 py-2 rounded-xl border border-stone-200 text-sm" />
              <input type="number" value={amount} onChange={e => setAmount(e.target.value)} placeholder="Amount NT$" className="w-24 px-3 py-2 rounded-xl border border-stone-200 text-sm" />
              <button type="submit" className="w-full mt-2 bg-stone-800 text-white py-2.5 rounded-xl text-sm font-semibold">Add Record</button>
            </form>
          </div>

          <div className="space-y-2.5">
            {current.map(t => (
              <div key={t.id} className="bg-white p-4 rounded-xl shadow-sm border border-stone-100 flex items-center justify-between">
                <div><p className="text-sm font-semibold">{t.description}</p><p className="text-[10px] text-stone-400">{new Intl.DateTimeFormat('en-US', { month: 'short', day: 'numeric' }).format(new Date(t.date))}</p></div>
                <div className="text-right">
                  <p className={`text-sm font-bold ${t.type === 'income' ? 'text-emerald-600' : 'text-rose-600'}`}>{t.type === 'income' ? '+' : '-'}NT$ {t.amount.toLocaleString()}</p>
                  {rate && <p className="text-[9px] text-stone-400">≈ Rp {(t.amount * rate).toLocaleString('id-ID', {maximumFractionDigits:0})}</p>}
                </div>
              </div>
            ))}
          </div>
        </div>
      );
    }

    // --- SUMMARY VIEW (KANBAN) ---
    function SummaryView({ tasks, goals, journals, transactions, currentDate }) {
      const isCur = (d) => d && new Date(d).getMonth() === currentDate.getMonth() && new Date(d).getFullYear() === currentDate.getFullYear();
      const mTasks = tasks.filter(t => isCur(t.date));
      const mGoals = goals.filter(g => isCur(g.date));
      const mJournals = journals.filter(j => isCur(j.date));
      const mFins = transactions.filter(t => isCur(t.date));

      const Col = ({ title, count, items, renderItem, colorClass, icon }) => (
        <div className="snap-center shrink-0 w-[280px] bg-white border border-stone-200 rounded-2xl shadow-sm p-4 flex flex-col h-[500px]">
          <div className="flex items-center gap-2 mb-4 pb-3 border-b border-stone-100">
            <div className={`p-1.5 rounded-lg ${colorClass}`}><Icon name={icon} size={16} /></div>
            <h3 className="font-bold text-stone-700">{title}</h3><span className="ml-auto text-xs font-bold text-stone-400">{count}</span>
          </div>
          <div className="overflow-y-auto space-y-3 flex-1 custom-scrollbar">
            {items.length === 0 ? <p className="text-xs text-stone-400 text-center py-8">Empty</p> : items.map(renderItem)}
          </div>
        </div>
      );

      const fDate = d => new Intl.DateTimeFormat('en-US', { month: 'short', day: 'numeric' }).format(new Date(d));

      return (
        <div className="space-y-4">
          <p className="text-xs text-stone-500 font-medium text-center animate-pulse">Swipe left/right 👉</p>
          <div className="flex overflow-x-auto gap-4 pb-4 snap-x custom-scrollbar">
            <Col title="To-Do List" count={mTasks.length} items={mTasks} colorClass="bg-[#8AA6A3]/20 text-[#6B8582]" icon="CheckSquare" renderItem={t => (
              <div key={t.id} className="p-3.5 bg-stone-50 rounded-xl border border-stone-100">
                <p className="text-sm font-medium">{t.text}</p>
                <div className="flex justify-between items-center mt-2 pt-2 border-t"><span className="text-[10px] text-stone-400">{fDate(t.date)}</span><span className={`text-[10px] font-bold ${t.completed ? 'text-emerald-600' : 'text-amber-500'}`}>{t.completed ? 'Done' : 'Pending'}</span></div>
              </div>
            )} />
            <Col title="Goals" count={mGoals.length} items={mGoals} colorClass="bg-[#D49A9A]/20 text-[#B07A7A]" icon="CalendarDays" renderItem={g => (
              <div key={g.id} className="p-3.5 bg-stone-50 rounded-xl border border-stone-100">
                <p className="text-sm font-medium">{g.text}</p>
                <div className="flex justify-between items-center mt-2 pt-2 border-t"><span className="text-[10px] text-stone-400">{fDate(g.date)}</span><span className={`text-[10px] font-bold ${g.completed ? 'text-emerald-600' : 'text-amber-500'}`}>{g.completed ? 'Done' : 'Pending'}</span></div>
              </div>
            )} />
            <Col title="Finances" count={mFins.length} items={mFins} colorClass="bg-[#5C838A]/20 text-[#405D62]" icon="Wallet" renderItem={f => (
              <div key={f.id} className="p-3.5 bg-stone-50 rounded-xl border border-stone-100">
                <p className="text-sm font-medium">{f.description}</p>
                <div className="flex justify-between items-center mt-2 pt-2 border-t"><span className="text-[10px] text-stone-400">{fDate(f.date)}</span><span className={`text-[10px] font-bold ${f.type==='income' ? 'text-emerald-600' : 'text-rose-500'}`}>{f.type==='income'?'+':'-'}NT${f.amount}</span></div>
              </div>
            )} />
            <Col title="Journals" count={mJournals.length} items={mJournals} colorClass="bg-[#D3C1A1]/20 text-[#A69578]" icon="BookOpen" renderItem={j => (
              <div key={j.id} className="p-3.5 bg-stone-50 rounded-xl border border-stone-100">
                <p className="text-sm font-medium line-clamp-3">{j.text}</p>
                <div className="flex justify-between items-center mt-2 pt-2 border-t"><span className="text-[10px] text-stone-400">{fDate(j.date)}</span><span className="text-[10px] font-bold text-stone-400">Logged</span></div>
              </div>
            )} />
          </div>
        </div>
      );
    }

    const root = ReactDOM.createRoot(document.getElementById('root'));
    root.render(<App />);
  </script>
</body>
</html>


```
