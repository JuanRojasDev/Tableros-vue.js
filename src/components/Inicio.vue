<script setup>
import { ref, reactive } from "vue";
import { v4 as uuidv4 } from "uuid";
import Input from "./Input.vue";
import ConfirmDialog from "./ConfirmDialog.vue";
import CreateDialog from "./CreateDialog.vue";

const count = ref(0);
const editingItem = ref(null);
let showCreateDialog = ref(false);
const boards = reactive([
  {
    id: uuidv4(),
    name: "tablero-1",
    items: [{ id: uuidv4(), title: "Hola a todos" }],
  },
]);

function findBoardAndItem(boardId, itemId) {
  const board = boards.find((board) => board.id === boardId);
  const item = board.items.find((item) => item.id === itemId);
  return { board, item };
}

let showConfirmDialogBoard = ref(false);
let showConfirmDialogItem = ref(false);
let itemToDelete = ref(null);
let boardToDelete = ref(null);

function deleteBoard(boardId) {
  boardToDelete.value = boardId;
  itemToDelete.value = null;
  showConfirmDialogBoard.value = true;
}

function deleteItem(boardId, itemId) {
  boardToDelete.value = boardId;
  itemToDelete.value = itemId;
  showConfirmDialogItem.value = true;
}

function confirmDelete() {
  if (boardToDelete.value && itemToDelete.value) {
    const { board } = findBoardAndItem(boardToDelete.value, itemToDelete.value);
    board.items = board.items.filter((item) => item.id !== itemToDelete.value);
  } else if (boardToDelete.value) {
    const index = boards.findIndex((board) => board.id === boardToDelete.value);
    if (index !== -1) {
      boards.splice(index, 1);
    }
  }
  boardToDelete.value = null;
  itemToDelete.value = null;
  showConfirmDialogBoard.value = false;
  showConfirmDialogItem.value = false;
}

function cancelDelete() {
  boardToDelete.value = null;
  itemToDelete.value = null;
  showConfirmDialogBoard.value = false;
  showConfirmDialogItem.value = false;
}

function startDrag(evt, boardId, itemId) {
  evt.dataTransfer.dropEffect = "move";
  evt.dataTransfer.effectAllowed = "move";
  evt.dataTransfer.setData("item", JSON.stringify({ boardId, itemId }));
}

function onDrop(evt, dest) {
  const { boardId, itemId } = JSON.parse(evt.dataTransfer.getData("item"));
  if (boardId === dest.id) return;
  const { board, item } = findBoardAndItem(boardId, itemId);
  board.items = board.items.filter((i) => i.id !== item.id);
  dest.items.push({ ...item });
}

function handleNewItem(text, board) {
  board.items.push({ id: uuidv4(), title: text });
}

function createNewBoard(name) {
  if (name) {
    const board = {
      id: uuidv4(),
      name: name,
      items: [],
    };

    boards.push(board);
  }
  showCreateDialog.value = false;
}

function showCreateBoardDialog() {
  showCreateDialog.value = true;
}

function cancelCreateBoard() {
  showCreateDialog.value = false;
}

function confirmDeleteItem() {
  const { board } = findBoardAndItem(boardToDelete.value, itemToDelete.value);
  board.items = board.items.filter((item) => item.id !== itemToDelete.value);
  showConfirmDialogItem.value = false;
}

function editItem(boardId, itemId) {
  editingItem.value = itemId;
}

function cancelEdit() {
  editingItem.value = null;
}

function updateItem(e, boardId, itemId) {
  if (e.key === "Enter") {
    const { item } = findBoardAndItem(boardId, itemId);
    item.title = e.target.value;
    editingItem.value = null;
  } else if (e.key === "Escape") {
    cancelEdit();
  }
}
</script>

<template>
    <ConfirmDialog
    :show="showConfirmDialogBoard"
    message="¿Estás seguro de que quieres eliminar el Tablero?"
    @confirm="confirmDelete"
    @cancel="cancelDelete"
  />
  <ConfirmDialog
    :show="showConfirmDialogItem"
    message="¿Estás seguro de que quieres eliminar este elemento?"
    @confirm="confirmDeleteItem"
    @cancel="cancelDelete"
  />
  <CreateDialog
    :show="showCreateDialog"
    @confirm="createNewBoard"
    @cancel="cancelCreateBoard"
  />
  <div>
    <header>
      <nav>
        <ul>
          <li>
            <a href="#" class="create-list" @click="showCreateBoardDialog">Crear un nuevo Tablero</a>
          </li>
        </ul>
      </nav>
    </header>

    <div class="boards-container">
      <div class="boards">
        <div
    class="board"
    @drop="onDrop($event, board)"
    @dragover.prevent
    @dragenter.prevent
    v-for="board in boards"
    :key="board.id"
  >
    <h2 class="board-title">
      {{ board.name }}
      <button class="delete-board" @click="deleteBoard(board.id)">
        x
      </button>
    </h2>

          <div class="input">
            <Input @on-new-item="(text) => handleNewItem(text, board)" />
          </div>
          <div
            class="board-item"
            draggable="true"
            @dragstart="startDrag($event, board.id, item.id)"
            v-for="item in board.items"
            :key="item.id"
          >
            <div v-if="item.id === editingItem" class="item-content">
              <input
                v-model="item.title"
                @blur="editingItem = null"
                @keyup.enter="updateItem($event, board.id, item.id)"
              />
            </div>
            <div v-else class="item-content">
              <div>{{ item.title }}</div>
              <div class="item-buttons">
                <button
                  class="delete-item"
                  @click="deleteItem(board.id, item.id)">Eliminar</button>
                <button class="edit-item" @click="editItem(board.id, item.id)">Editar</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
@import "../assets/main.css";
</style>
