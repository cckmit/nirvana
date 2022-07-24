---
title: Iterator模式
date: 2021-03-11 21:12:40
categories: 设计模式（TypeScript）
---
将（Book）放置到书架（BookShelf）中，并将书名按顺序显示

## 接口：
```
import Book from "./Book";
//Aggregate接口是所要遍历的集合的接口
export interface Aggregate {
    bookIterator: BookIterator;
}
//Iterator接口，用于遍历集合中的元素
export interface BookIterator {
    hasNext(): boolean;//是否有下一个
    next(): Book;//获取下一个元素
}
```
#Book类：
```
/**
 * class Book
 */
export default class Book {
    private name: string;
    /**
     * Book
     */
    constructor(name: string) {
        this.name = name;
    }
    public getName(): string {
        return this.name
    }
}
```
#BookShelf类：
```
import Book from "./Book";
import { BookIterator } from "./Aggregate";
import BookShelfIterator from "./BookShelfIterator";
export default class BookShelf {
    private books: Array<Book>;
    private last: number = 0;
    constructor(maxsize: number) {
        this.books = new Array<Book>(maxsize);
    }
    /**
     * appendBook
     */
    public appendBook(book: Book): void {
        this.books[this.last] = book;
        this.last++;
    }
    /**
     * getBookAt
     */
    public getBookAt(index: number): Book {
        return this.books[index]
    }
    /**
     * getLength
     */
    public getLength(): number {
        return this.last;
    }

    /**
     * name
     */
    public bookIterator(): BookIterator {
        return new BookShelfIterator(this);
    }
}
```
#BookShelfIterator类
```
 import BookShelf from "./BookShelf";
import Book from "./Book";
import { BookIterator } from "./Aggregate";
export default class BookShelfIterator implements BookIterator {
    private bookShelf: BookShelf;
    private index: number;
    constructor(bookShelf: BookShelf) {
        this.bookShelf = bookShelf;
        this.index = 0;
    }

    /**
     * hasNext
     */
    public hasNext(): boolean {
        if (this.index < this.bookShelf.getLength()) {
            return true;
        } else {
            return false;
        }
    }
    /**
     * next
     */
    public next(): Book {
        const book = this.bookShelf.getBookAt(this.index);
        this.index++;
        return book;
    }
}
```
#index入口
```
import BookShelf from "./BookShelf";
import Book from "./Book";

let bookShelf = new BookShelf(4);
bookShelf.appendBook(new Book("test1"));
bookShelf.appendBook(new Book("test2"));
bookShelf.appendBook(new Book("test3"));
bookShelf.appendBook(new Book("test4"));
const it = bookShelf.bookIterator();
while (it.hasNext()) {
    const book = it.next();
    console.log(book.getName());
}
```
