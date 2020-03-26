# repository5

type
  PNode = ^TNode;
  TNode = record
    key: integer;
    balance: integer;
    left, right: PNode;
  end;

function Max(a, b: integer): integer;
begin
  Result := a;
  if a <= b then Result := b;
end;

function Height(root: PNode): integer;
begin
  if root = nil then
    Result := -1
  else
    Result := 1 + Max(Height(root^.left), Height(root^.right));
end;

procedure Add(var root: PNode; key: integer);
begin
  if root = nil then
  begin
    New(root);
    root^.key := key;
    root^.balance := 0;
    root^.left := nil;
    root^.right := nil;
  end
  else
  if root^.key > key then
    Add(root^.left, key)
  else 
    Add(root^.right, key);
    root^.balance := Height(root^.left) - Height(root^.right);
end;

procedure Print(root: PNode; height: integer );
var i: integer;
begin
  if root <> nil then
  begin
     Print(root^.right, height + 1);
     for i:=1 to height do Write(#9);
     Writeln(root^.key,' (bal=', root^.balance,')');
     Print(root^.left, height + 1);
  end;
end;

procedure DeleteTree(var root: PNode);
begin
  if root <> nil then
  begin
    DeleteTree(root^.left);
    DeleteTree(root^.right);
    Dispose(root);
    root := nil;
  end;
end;

function IsAvl(root: PNode): boolean;
begin
  Result := true;
  if root <> nil then
    Result := IsAvl(root^.left) and IsAvl(root^.right)
      and (Abs(Height(root^.left) - Height(root^.right)) < 2);
end;

procedure MakeAvl(var root: PNode);
var a,b,p: PNode;
begin
  if root <> nil then
  begin
    MakeAvl(root^.left);
    MakeAvl(root^.right);
    root^.balance := Height(root^.left) - Height(root^.right);
    while Abs(root^.balance) >= 2 do
    begin
      //правое поддерево длиннее
      if root^.balance < -1 then
      begin
        a := root^.right;
        //правое поддерево длиннее или одинаковой высоты с левым
        if a^.balance <= 0 then
        begin
          p := root;
          root := a;
          p^.right := a^.left;
          MakeAvl(p);
          root^.left := p;
          
          root^.balance := Height(root^.left) - Height(root^.right);
        end
        else
        begin
          b := a^.left;
          p := root;
          p^.right := b^.left;
          a^.left := b^.right;
          root := b;
          root^.right := a;
          if b^.balance > 0 then a^.balance := -1 else a^.balance := 0;
          MakeAvl(p);
          root^.left := p;
          root^.balance := Height(root^.left) - Height(root^.right);
        end;
      end
      else //if root^.balance>1  левое поддерево длиннее
      begin
        a := root^.left;
        //левое поддерево длиннее или одинаковой высоты с правым
        if a^.balance >= 0 then
        begin
          p := root;
          root := a;
          p^.left := a^.right;
          MakeAvl(p);
          root^.right := p;
          
          root^.balance := Height(root^.left) - Height(root^.right);
        end
        else
        begin
          b := a^.right;
          p := root;
          p^.left := b^.right;
          a^.right := b^.left;
          root := b;
          root^.left := a;
          if b^.balance <0  then a^.balance := 1 else a^.balance := 0;
          MakeAvl(p);
          root^.right := p;
          root^.balance := Height(root^.left) - Height(root^.right);
        end;
      end;
    end;
  end;
end;


var
  root: PNode = nil;
  i: integer;
begin
  Add(root,8);
  for i:=9 downto 1 do
    if i <> 8 then
      Add(root, i);
  Print(root,1);
  Writeln('Height = ', Height(root));
  Writeln('IsAvl = ', IsAvl(root));

  Writeln;
  Writeln;
  MakeAvl(root);
  Writeln('Avl Tree');
  Writeln('---------------------------------');
  Print(root,1);
  Writeln('IsAvl = ', IsAvl(root));
  DeleteTree(root);
  Readln;
end.
