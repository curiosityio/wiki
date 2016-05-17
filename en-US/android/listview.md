---
name: ListView
---

# Adapter

```
import android.content.Context;
import android.support.v4.content.ContextCompat;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ArrayAdapter;
import android.widget.ImageView;
import android.widget.TextView;

import java.util.ArrayList;

public class ListViewAdapter extends ArrayAdapter<ItemVo> {

    private Context mContext;
    private ArrayList<ItemVo> mData;

    public NavDrawerListViewAdapter(Context context, ArrayList<ItemVo> objects) {
        super(context, R.layout.adapter_listview_row, objects);

        mContext = context;
        mData = objects;
    }

    static class ViewHolder {
        public ImageView icon;
        public TextView title;
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View rowView = convertView;

        if (rowView == null) {
            LayoutInflater inflater = (LayoutInflater) mContext.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
            rowView = inflater.inflate(R.layout.adapter_listview_row, parent, false);

            ViewHolder viewHolder = new ViewHolder();
            viewHolder.icon = (ImageView) rowView.findViewById(R.id.row_item_icon);
            viewHolder.title = (TextView) rowView.findViewById(R.id.row_title_textview);

            rowView.setTag(viewHolder);
        }

        ViewHolder viewHolder = (ViewHolder) rowView.getTag();

        final ItemVo rowItem = mData.get(position);

        viewHolder.icon.setImageResource(rowItem.drawableRes);
        viewHolder.title.setText(rowItem.title);

        return rowView;
    }

}
```
