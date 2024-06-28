# Todo_App
import { useEffect, useState } from 'react';
import Styles from './TODO.module.css';
import { dummy } from './dummy';
import axios from 'axios';

export function TODO(props) {
    const [newTodo, setNewTodo] = useState('');
    const [newDescription, setNewDescription] = useState('');
    const [todoData, setTodoData] = useState(dummy);
    const [loading, setLoading] = useState(true);
    const [editMode, setEditMode] = useState(false);
    const [editId, setEditId] = useState(null);
    const [editTodo, setEditTodo] = useState('');
    const [editDescription, setEditDescription] = useState('');

    useEffect(() => {
        const fetchTodo = async () => {
            const apiData = await getTodo();
            setTodoData(apiData);
            setLoading(false);
        };
        fetchTodo();
    }, []);

    const getTodo = async () => {
        const options = {
            method: "GET",
            url: http://localhost:8000/api/todo,
            headers: {
                accept: "application/json",
            }
        };
        try {
            const response = await axios.request(options);
            return response.data;
        } catch (err) {
            console.log(err);
            return []; // return an empty array in case of error
        }
    };

    const addTodo = () => {
        const options = {
            method: "POST",
            url: http://localhost:8000/api/todo,
            headers: {
                accept: "application/json",
            },
            data: {
                title: newTodo,
                description: newDescription
            }
        };
        axios
            .request(options)
            .then(function (response) {
                console.log(response.data);
                setTodoData(prevData => [...prevData, response.data.newTodo]);
                setNewTodo('');
                setNewDescription('');
            })
            .catch((err) => {
                console.log(err);
            });
    };

    const deleteTodo = (id) => {
        const options = {
            method: "DELETE",
            url: http://localhost:8000/api/todo/${id},
            headers: {
                accept: "application/json",
            }
        };
        axios
            .request(options)
            .then(function (response) {
                console.log(response.data);
                setTodoData(prevData => prevData.filter(todo => todo._id !== id));
            })
            .catch((err) => {
                console.log(err);
            });
    };

    const updateTodo = (id) => {
        const todoToUpdate = todoData.find(todo => todo._id === id);
        const options = {
            method: "PATCH",
            url: http://localhost:8000/api/todo/${id},
            headers: {
                accept: "application/json",
            },
            data: {
                ...todoToUpdate,
                done: !todoToUpdate.done
            }
        };
        axios
            .request(options)
            .then(function (response) {
                console.log(response.data);
                setTodoData(prevData => prevData.map(todo => todo._id === id ? response.data : todo));
            })
            .catch((err) => {
                console.log(err);
            });
    };

    const handleEditClick = (entry) => {
        setEditMode(true);
        setEditId(entry._id);
        setEditTodo(entry.title);
        setEditDescription(entry.description);
    };

    const saveEdit = () => {
        const options = {
            method: "PATCH",
            url: http://localhost:8000/api/todo/${editId},
            headers: {
                accept: "application/json",
            },
            data: {
                title: editTodo,
                description: editDescription
            }
        };
        axios
            .request(options)
            .then(function (response) {
                console.log(response.data);
                setTodoData(prevData => prevData.map(todo => todo._id === editId ? response.data : todo));
                setEditMode(false);
                setEditId(null);
                setEditTodo('');
                setEditDescription('');
            })
            .catch((err) => {
                console.log(err);
            });
    };

    return (
        <div className={Styles.ancestorContainer}>
            <div className={Styles.headerContainer}>
                <h1>Tasks</h1>
                <span>
                    <input
                        className={Styles.todoInput}
                        type='text'
                        name='New Todo'
                        value={newTodo}
                        placeholder='Title'
                        onChange={(event) => setNewTodo(event.target.value)}
                    />
                    <input
                        className={Styles.todoInput}
                        type='text'
                        name='New Description'
                        value={newDescription}
                        placeholder='Description'
                        onChange={(event) => setNewDescription(event.target.value)}
                    />
                    <button
                        id='addButton'
                        name='add'
                        className={Styles.addButton}
                        onClick={addTodo}
                    >
                        + New Todo
                    </button>
                </span>
            </div>
            <div id='todoContainer' className={Styles.todoContainer}>
                {loading ? (
                    <p style={{ color: 'white' }}>Loading...</p>
                ) : (
                    todoData.length > 0 ? (
                        todoData.map((entry) => (
                            <div key={entry._id} className={Styles.todo}>
                                {editMode && editId === entry._id ? (
                                    <>
                                        <input
                                            type='text'
                                            value={editTodo}
                                            onChange={(e) => setEditTodo(e.target.value)}
                                            placeholder='Edit Title'
                                        />
                                        <input
                                            type='text'
                                            value={editDescription}
                                            onChange={(e) => setEditDescription(e.target.value)}
                                            placeholder='Edit Description'
                                        />
                                        <button onClick={saveEdit}>Save</button>
                                        <button onClick={() => setEditMode(false)}>Cancel</button>
                                    </>
                                ) : (
                                    <>
                                        <span className={Styles.infoContainer}>
                                            <input
                                                type='checkbox'
                                                checked={entry.done}
                                                onChange={() => updateTodo(entry._id)}
                                            />
                                            {entry.title}
                                        </span>
                                        <span
                                            style={{ cursor: 'pointer' }}
                                            onClick={() => deleteTodo(entry._id)}
                                        >
                                            Delete
                                        </span>
                                        <span
                                            className={Styles.editButton}
                                            style={{ cursor: 'pointer' }}
                                            onClick={() => handleEditClick(entry)}
                                        >
                                            Edit
                                        </span>
                                        <span className={Styles.todoDescription}>
                                            {entry.description}
                                        </span>
                                    </>
                                )}
                            </div>
                        ))
                    ) : (
                        <p className={Styles.noTodoMessage}>No tasks available. Please add a new task.</p>
                    )
                )}
            </div>
        </div>
    );
}
