# to-do-list
import React, { useState, useEffect } from 'react';

const TodoList = () => {
  const [todos, setTodos] = useState([]);
  const [newTodo, setNewTodo] = useState('');
  const [filter, setFilter] = useState('all'); // 'all', 'completed', 'pending'

  // Load tasks from localStorage
  useEffect(() => {
    const storedTodos = localStorage.getItem('todos');
    if (storedTodos) {
      setTodos(JSON.parse(storedTodos));
    }
  }, []);

  // Save tasks to localStorage
  useEffect(() => {
    localStorage.setItem('todos', JSON.stringify(todos));
  }, [todos]);

  const handleAddTodo = (e) => {
    e.preventDefault();
    if (newTodo.trim()) { // Validate input
      setTodos([...todos, { text: newTodo, completed: false }]);
      setNewTodo('');
    }
  };

  const handleRemoveTodo = (index) => {
    const newTodos = [...todos];
    newTodos.splice(index, 1);
    setTodos(newTodos);
  };

  const handleMarkComplete = (index) => {
    const newTodos = [...todos];
    newTodos[index].completed = !newTodos[index].completed;
    setTodos(newTodos);
  };

  const handleFilterChange = (e) => {
    setFilter(e.target.value);
  };

  const getFilteredTodos = () => {
    switch (filter) {
      case 'completed':
        return todos.filter((todo) => todo.completed);
      case 'pending':
        return todos.filter((todo) => !todo.completed);
      default:
        return todos;
    }
  };

  const renderTodos = () => {
    const filteredTodos = getFilteredTodos();
    return filteredTodos.map((todo, index) => (
      <div key={index} className="todo-item">
        <input
          type="checkbox"
          checked={todo.completed}
          onChange={() => handleMarkComplete(index)}
        />
        <span className={todo.completed ? 'completed' : ''}>{todo.text}</span>
        <button onClick={() => handleRemoveTodo(index)}>Remove</button>
      </div>
    ));
  };

  return (
    <div className="todo-list">
      <h1>To-Do List</h1>
      <form onSubmit={handleAddTodo}>
        <input
          type="text"
          value={newTodo}
          onChange={(e) => setNewTodo(e.target.value)}
          placeholder="Add a new todo..."
        />
        <button type="submit">Add</button>
      </form>
      <div>
        Filter:
        <select value={filter} onChange={handleFilterChange}>
          <option value="all">All</option>
          <option value="completed">Completed</option>
          <option value="pending">Pending</option>
        </select>
      </div>
      {renderTodos()}
    </div>
  );
};

export default TodoList;
