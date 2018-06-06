
```
public class TwoWayMap<A,B> {
    private Map<A,B> mMapA;
    private Map<B,A> mMapB;

    public TwoWayMap() {
        mMapA=new HashMap<>();
        mMapB=new HashMap<>();
    }

    public void put(A a, B b){
        if (mMapA.get(a)==null&&mMapB.get(b)==null){
            mMapA.put(a,b);
            mMapB.put(b,a);
            return;
        }
        if (mMapA.get(a)==null&&mMapB.get(b)!=null){
            A c=mMapB.get(b);
            mMapA.put(a,b);
            mMapA.remove(c);
            mMapB.put(b,a);
            return;
        }
        if (mMapA.get(a)!=null&&mMapB.get(b)==null){
            B c=mMapA.get(a);
            mMapA.put(a, b);
            mMapB.remove(c);
            mMapB.put(b,a);
            return;
        }
    }

    public void remove(Object o){
        if (mMapA.get(o)!=null){
            B b=mMapA.get(o);
            mMapA.remove(o);
            mMapB.remove(b);
        }

        if (mMapB.get(o)!=null){
            A a=mMapB.get(o);
            mMapA.remove(a);
            mMapB.remove(o);
        }

    }

    public void clear(){
        mMapB.clear();
        mMapA.clear();
    }

    public B getB(A a){
        return mMapA.get(a);
    }
    public A getA(B b){
        return mMapB.get(b);
    }



    public List<A> getAList(){
        List<A> list=new ArrayList<>();
        for (A a:mMapB.values()) {
            list.add(a);
        }
        return list;
    }

    public List<B> getBList(){
        List<B> list=new ArrayList<>();
        for (B b:mMapA.values()) {
            list.add(b);
        }
        return list;
    }


    public int size(){
        return mMapA.size();
    }

    @Override
    public String toString() {
        return mMapA.toString();
    }
}
```
