```css
┌────────────────────┐
│ ELF Header         │ ← 文件元数据
├────────────────────┤
│ Program Header     │ ← runtime 使用（装载器）
├────────────────────┤
│ Section Header     │ ← link-time 使用（链接器）
├────────────────────┤
│          Segments  │ ← 可加载到内存的段 (.text .data 等)
│          Sections  │ ← 编译器/链接器用 (.symtab .strtab)
└────────────────────┘

```

