---
name: DBFlow ORM library.
---

# How to run queries.
All examples came from [offical doc](https://github.com/Raizlabs/DBFlow/blob/master/usage/SQLQuery.md). More examples there.

* [Count query](#count-query)
* [Select query](#select-query)
* [Update query](#update-query)
* [Errors](#errors)

# Count query

How many users have the username "levi"?
```
long count = SQLite.selectCountOf().where(UserModel_Table.username.is("levi")).count();
```

# Select query

This example gets all users with username "levi".
```
List<UserModel> users = SQLite.select().from(UserModel.class).where(UserModel_Table.username.is("levi")).queryList();
```
This run synchronously. It returns a list of results.

If you want to return just 1 result:
```
UserModel user = SQLite.select().from(UserModel.class).where(UserModel_Table.username.is("levi")).querySingle();
```

# Update query

This example updates the UserModel username for a user to "levi" where it is currently set at "sam".
```
SQLite.update(UserModel.class)
              .set(UserModel_Table.username.eq("levi"))
              .where(UserModel_Table.username.is("sam")).query();
```
This run synchronously.

# Errors

* **...is not registered with a Database. Did you forget the @Table annotation?**

When using DBFlow you get this error often but not every time. [This issue](https://github.com/Raizlabs/DBFlow/issues/685) goes over the issue and solution. Long story short, from command line (not Android Studio) run `./gradlew clean`, then build/install/run from studio again. It may work, but cannot guarantee it. You have to keep cleaning until finally it works again.

When you make changes to any of the DBFlow table/database/dao you need to do a full clean in order for the changes to happen.

# Using RecyclerView with DBFlow

* Add this file to your project's adapter package. It is a custom recyclerview adapter made for DBFlow cursor lists:

```
package your.package.name.here;

import android.database.ContentObserver;
import android.database.Cursor;
import android.database.DataSetObserver;
import android.os.Handler;
import android.support.v7.widget.RecyclerView;
import android.widget.Filter;
import android.widget.FilterQueryProvider;
import android.widget.Filterable;

public abstract class FlowCursorListRecyclerAdapter<VH extends android.support.v7.widget.RecyclerView.ViewHolder> extends RecyclerView.Adapter<VH> implements Filterable, CursorFilter.CursorFilterClient {

    private static final int INVALID_ROW_ID_COLUMN = -1;

    private static final String PRIMARY_KEY_COLUMN_NAME = "id";

	private boolean mDataValid;
	private int mRowIDColumn = INVALID_ROW_ID_COLUMN;
	private Cursor mCursor;
	private ChangeObserver mChangeObserver;
	private DataSetObserver mDataSetObserver;
	private CursorFilter mCursorFilter;
	private FilterQueryProvider mFilterQueryProvider;

	public FlowCursorListRecyclerAdapter() {
		init();
	}

	void init() {
        setHasStableIds(true);

		mChangeObserver = new ChangeObserver();
		mDataSetObserver = new MyDataSetObserver();
	}

	@Override
	public void onBindViewHolder(VH holder, int i){
		if (!mDataValid) {
			throw new IllegalStateException("this should only be called when the cursor is valid");
		}
		if (!mCursor.moveToPosition(i)) {
			throw new IllegalStateException("couldn't move cursor to position " + i);
		}
		onBindViewHolderCursor(holder, i);
	}

	public abstract void onBindViewHolderCursor(VH holder, int position);

	@Override
	public final int getItemCount() {
		if (mDataValid && mCursor != null) {
			return mCursor.getCount();
		} else {
			return 0;
		}
	}

	@Override
	public long getItemId(int position) {
		if (mDataValid && mCursor != null) {
			if (mCursor.moveToPosition(position)) {
				return mCursor.getLong(mRowIDColumn);
			} else {
				return 0;
			}
		} else {
			return 0;
		}
	}

	public Cursor getCursor() {
		return mCursor;
	}

	public void changeCursor(Cursor cursor) {
		Cursor old = swapCursor(cursor);
		if (old != null) {
			old.close();
		}
	}

	public Cursor swapCursor(Cursor newCursor) {
		if (newCursor == mCursor) {
			return null;
		}
		Cursor oldCursor = mCursor;
		if (oldCursor != null) {
			if (mChangeObserver != null) oldCursor.unregisterContentObserver(mChangeObserver);
			if (mDataSetObserver != null) oldCursor.unregisterDataSetObserver(mDataSetObserver);
		}
		mCursor = newCursor;
		if (newCursor != null) {
			if (mChangeObserver != null) newCursor.registerContentObserver(mChangeObserver);
			if (mDataSetObserver != null) newCursor.registerDataSetObserver(mDataSetObserver);
			mRowIDColumn = newCursor.getColumnIndexOrThrow(PRIMARY_KEY_COLUMN_NAME);
			mDataValid = true;

			notifyDataSetChanged();
		} else {
			mRowIDColumn = INVALID_ROW_ID_COLUMN;
			mDataValid = false;

			notifyItemRangeRemoved(0, getItemCount());
		}
		return oldCursor;
	}

	public CharSequence convertToString(Cursor cursor) {
		return cursor == null ? "" : cursor.toString();
	}

	public Cursor runQueryOnBackgroundThread(CharSequence constraint) {
		if (mFilterQueryProvider != null) {
			return mFilterQueryProvider.runQuery(constraint);
		}

		return mCursor;
	}

	public Filter getFilter() {
		if (mCursorFilter == null) {
			mCursorFilter = new CursorFilter(this);
		}

		return mCursorFilter;
	}

	public FilterQueryProvider getFilterQueryProvider() {
		return mFilterQueryProvider;
	}

	public void setFilterQueryProvider(FilterQueryProvider filterQueryProvider) {
		mFilterQueryProvider = filterQueryProvider;
	}

	protected void onContentChanged() {
	}

	private class ChangeObserver extends ContentObserver {
		public ChangeObserver() {
			super(new Handler());
		}

		@Override
		public boolean deliverSelfNotifications() {
			return true;
		}

		@Override
		public void onChange(boolean selfChange) {
			onContentChanged();
		}
	}

	private class MyDataSetObserver extends DataSetObserver {
		@Override
		public void onChanged() {
			mDataValid = true;
			notifyDataSetChanged();
		}

		@Override
		public void onInvalidated() {
			mDataValid = false;

			notifyItemRangeRemoved(0, getItemCount());
		}
	}
}

class CursorFilter extends Filter {

	CursorFilterClient mClient;

	interface CursorFilterClient {
		CharSequence convertToString(Cursor cursor);
		Cursor runQueryOnBackgroundThread(CharSequence constraint);
		Cursor getCursor();
		void changeCursor(Cursor cursor);
	}

	CursorFilter(CursorFilterClient client) {
		mClient = client;
	}

	@Override
	public CharSequence convertResultToString(Object resultValue) {
		return mClient.convertToString((Cursor) resultValue);
	}

	@Override
	protected FilterResults performFiltering(CharSequence constraint) {
		Cursor cursor = mClient.runQueryOnBackgroundThread(constraint);

		FilterResults results = new FilterResults();
		if (cursor != null) {
			results.count = cursor.getCount();
			results.values = cursor;
		} else {
			results.count = 0;
			results.values = null;
		}
		return results;
	}

	@Override
	protected void publishResults(CharSequence constraint, FilterResults results) {
		Cursor oldCursor = mClient.getCursor();

		if (results.values != null && results.values != oldCursor) {
			mClient.changeCursor((Cursor) results.values);
		}
	}
}
```

* Create an adapter for the recyclerview.

```
package your.package.name.here;

import android.support.v7.widget.RecyclerView;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.Button;
import android.widget.ImageButton;
import android.widget.TextView;
import com.raizlabs.android.dbflow.list.FlowCursorList;

public class InboxListAdapter extends FlowCursorListRecyclerAdapter<InboxListAdapter.ViewHolder> {

    private FlowCursorList<EmailModel> mFlowCursorList;
    private AdapterListener mListener;

    public static class ViewHolder extends RecyclerView.ViewHolder {

        private TextView mPositionTitleTextView;

        public ViewHolder(View view) {
            super(view);

            mPositionTitleTextView = (TextView) view.findViewById(R.id.position_title_textview);
        }
    }

    public interface AdapterListener {
        void openButtonPressed(int position, EmailModel data, View button);
    }

    public InboxListAdapter(AdapterListener listener) {
        mListener = listener;
    }

    public void setCursorList(FlowCursorList<EmailModel> cursorList) {
        mFlowCursorList = cursorList;

        if (cursorList != null) {
            swapCursor(cursorList.getCursor());
        } else {
            swapCursor(null);
        }
    }

    @Override
    public InboxListAdapter.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.inbox_list_row, parent, false);

        return new ViewHolder(view);
    }

    @Override
    public void onBindViewHolderCursor(final ViewHolder holder, final int position) {
        final EmailModel model = mFlowCursorList.getItem(position);

        holder.mPositionTitleTextView.setText(model.title);

        holder.mPositionTitleTextView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                mListener.openButtonPressed(holder.getAdapterPosition(), mFlowCursorList.getItem(holder.getAdapterPosition()), v);
            }
        });
    }

}
```

* In Fragment or Activity, create a loader to load the DAO data and set the adapter.

```
package your.package.name.here;

import android.app.LoaderManager;
import android.content.AsyncTaskLoader;
import android.content.Loader;
import android.os.Bundle;
import android.support.annotation.NonNull;
import android.support.design.widget.Snackbar;
import android.support.v4.widget.SwipeRefreshLayout;
import android.support.v7.widget.LinearLayoutManager;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import com.raizlabs.android.dbflow.list.FlowCursorList;
import org.greenrobot.eventbus.EventBus;
import org.greenrobot.eventbus.Subscribe;

import javax.inject.Inject;

public class InboxListFragment extends BaseListFragment implements InboxListAdapter.AdapterListener, LoaderManager.LoaderCallbacks<FlowCursorList<EmailModel>> {

    private static final int INBOX_FLOW_CURSOR_LIST_LOADER_ID = 0;

    private InfiniteScrollRecyclerView mInboxList;
    private InboxListEmptyView mInboxListEmptyView;
    private SwipeRefreshLayout mSwipeRefreshLayout;
    private View mLoadingMoreDataView;
    private View mLoadingInboxView;

    private InboxListAdapter mInboxListAdapter;

    @Inject EmailsDao mEmailsDao;
    @Inject EmailsApiManager mEmailsApiManager;

    public static InboxListFragment newInstance() {
        InboxListFragment fragment = new InboxListFragment();

        return fragment;
    }

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        MainApplication.component().inject(this);
    }

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.fragment_inbox, container, false);

        mInboxList = (InfiniteScrollRecyclerView) view.findViewById(R.id.inbox_recycler_view);
        mInboxListEmptyView = (InboxListEmptyView) view.findViewById(R.id.inbox_list_empty_view);
        mSwipeRefreshLayout = (SwipeRefreshLayout) view.findViewById(R.id.inbox_swipe_refresh_list);
        mLoadingMoreDataView = view.findViewById(R.id.inbox_loading_more_data_spinner);
        mLoadingInboxView = view.findViewById(R.id.inbox_list_loading_view);

        setupViews();

        getLoaderManager().initLoader(INBOX_FLOW_CURSOR_LIST_LOADER_ID, null, this);

        return view;
    }

    private void setupViews() {
        showLoadingListView();
        hideLoadingMoreDataView();

        mSwipeRefreshLayout.setOnRefreshListener(new SwipeRefreshLayout.OnRefreshListener() {
            @Override
            public void onRefresh() {
                mListListener.refreshTriggered();
            }
        });
        mInboxListAdapter = new InboxListAdapter(this);
        mInboxList.setLayoutManager(new LinearLayoutManager(getActivity()));
        mInboxList.setAdapter(mInboxListAdapter);
        mInboxList.setScrollListener(new InfiniteScrollRecyclerView.ScrollListener() {
            @Override
            public void loadMore() {
                showLoadingMoreDataView();

                mListListener.loadMore();
            }
        });
    }

    private void showLoadingMoreDataView() {
        mLoadingMoreDataView.setVisibility(View.VISIBLE);
    }

    private void hideLoadingMoreDataView() {
        mLoadingMoreDataView.setVisibility(View.GONE);
    }

    private void showLoadingListView() {
        mLoadingInboxView.setVisibility(View.VISIBLE);

        mInboxListEmptyView.setVisibility(View.GONE);
    }

    private void showListOrEmptyView() {
        mLoadingInboxView.setVisibility(View.GONE);

        if (mInboxListAdapter.getItemCount() <= 0) {
            mInboxListEmptyView.setVisibility(View.VISIBLE);
        } else {
            mInboxListEmptyView.setVisibility(View.GONE);
        }
    }

    @Override
    public void openButtonPressed(int position, EmailModel email, View button) {
        startActivity(IntentUtil.getOpenWebpageUrlIntent(email.link));
    }

    private void refreshInboxList() {
        getLoaderManager().restartLoader(INBOX_FLOW_CURSOR_LIST_LOADER_ID, null, this);

        showListOrEmptyView();
    }

    @Override
    public Loader<FlowCursorList<EmailModel>> onCreateLoader(int id, Bundle args) {
        return new AsyncTaskLoader<FlowCursorList<EmailModel>>(getActivity()) {
            private FlowCursorList<EmailModel> mFlowCursorList;

            @Override
            protected void onStartLoading() {
                if (mFlowCursorList == null) {
                    forceLoad();
                }

                super.onStartLoading();
            }

            @Override
            public FlowCursorList<EmailModel> loadInBackground() {
                mFlowCursorList = mEmailsDao.getInboxFlowCursorList();

                return mFlowCursorList;
            }
        };
    }

    @Override
    public void onLoadFinished(Loader<FlowCursorList<EmailModel>> loader, FlowCursorList<EmailModel> data) {
        mInboxListAdapter.setCursorList(data);

        showListOrEmptyView();
    }

    @Override
    public void onLoaderReset(Loader<FlowCursorList<EmailModel>> loader) {
        mInboxListAdapter.setCursorList(null);
    }

}
```

In case you want it, here is the InfiniteScrollRecyclerView:

```
package your.package.name.here;

import android.content.Context;
import android.support.annotation.Nullable;
import android.support.v7.widget.LinearLayoutManager;
import android.support.v7.widget.RecyclerView;
import android.util.AttributeSet;

public class InfiniteScrollRecyclerView extends RecyclerView {

    public interface ScrollListener {
        void loadMore();
    }

    private ScrollListener mScrollListener;

    private int mPreviousListCountTotal = 0;
    private boolean mLoadingMoreData = true;
    private int mVisibleThresholdToLoadMoreData = 5;

    private boolean mMoreDataToLoad = true;

    public InfiniteScrollRecyclerView(Context context) {
        super(context);
    }

    public InfiniteScrollRecyclerView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
    }

    public InfiniteScrollRecyclerView(Context context, @Nullable AttributeSet attrs, int defStyle) {
        super(context, attrs, defStyle);
    }

    public void setScrollListener(ScrollListener listener) {
        mScrollListener = listener;

        if (mScrollListener == null) {
            mMoreDataToLoad = false;
        }

        addOnScrollListener(new RecyclerView.OnScrollListener() {

            @Override
            public void onScrolled(RecyclerView recyclerView, int dx, int dy) {
                super.onScrolled(recyclerView, dx, dy);

                int firstVisibleItem, visibleItemCount, totalItemCount;

                if (getLayoutManager() == null) {
                    throw new RuntimeException("You must set the layout manager.");
                }

                if (!(getLayoutManager() instanceof LinearLayoutManager)) {
                    throw new RuntimeException(getClass().getSimpleName() + " only works with LinearLayoutManager for the layout manager at the moment.");
                }

                visibleItemCount = getChildCount();
                totalItemCount = getLayoutManager().getItemCount();
                firstVisibleItem = ((LinearLayoutManager)getLayoutManager()).findFirstVisibleItemPosition();

                if (mLoadingMoreData) {
                    if (totalItemCount > mPreviousListCountTotal) {
                        mLoadingMoreData = false;
                        mPreviousListCountTotal = totalItemCount;
                    }
                }
                if (!mLoadingMoreData && (totalItemCount - visibleItemCount) <= (firstVisibleItem + mVisibleThresholdToLoadMoreData)) {
                    mLoadingMoreData = true;

                    if (mMoreDataToLoad) {
                        mScrollListener.loadMore();
                    }
                }
            }
        });
    }

}
```
